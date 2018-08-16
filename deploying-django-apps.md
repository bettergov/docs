---
description: >-
  For applications requiring a CMS, we use Django. For sufficiently small,
  internal-facing projects, we deploy to AWS Lightsail ($5/mo).
---

# Deploying Django apps

The deployment steps here are best practices cobbled together from a variety of sources, primarily [Michał Karzyński](http://michal.karzynski.pl/blog/2013/06/09/django-nginx-gunicorn-virtualenv-supervisor/) and [Coding Startups](https://codingstartups.com/deploy-django-nginx-gunicorn-postgresql-supervisor/). This is meant as a blueprint, not as gospel.

### Assumptions

* You've developed a functional Django app ready for deployment
* That project is uploaded to a remote Git repository
* That project has its Python dependencies frozen in `requirements.txt`

Recommended reading: Django's [Quick install guide](https://www.djangoproject.com/start/) and [Writing your first Django app](https://docs.djangoproject.com/en/2.1/intro/tutorial01/); Coding Startups' [3 best practices for better setting up your Django project](https://codingstartups.com/3-best-practices-better-setting-django-project/)

### Outline

We are going to deploy the Django project using the following steps:

1. Create a new AWS Lightsail instance
2. Install prerequisites: pip, pipenv, git
3. Set up a new Linux user
4. Pull the Django app from Git
5. Set up PostgreSQL
6. Configure Gunicorn \(gateway\) with Supervisor
7. Configure Nginx \(server\) for listening to requests
8. Secure our deployed app: set up firewall

We will use [bga\_chartwerk](https://github.com/bettergov/bga_chartwerk) as our example app.

### Create a new instance

An instance in AWS Lightsail refers to a virtual private server with CPU, RAM and disk space. We will:

* Create the instance
* Attach a static IP
* Configure DNS records with the static IP
* Connect to the instance via SSH

Creating an instance is straightforward. Simply go the [Lightsail dashboard](https://lightsail.aws.amazon.com) and click 'Create instance.'

* **Instance location:** Use the default.
* **Pick your instance image:** OS Only &gt; Ubuntu
* **SSH key pair:** Either upload and select your own public key, or download Lightsail's default private key.
* **Choose your instance plan:** The $5/mo plan should be sufficient.
* **Name your instance:** I tend to name the instance whatever domain name it will be accessed at \(e.g. chartwerk.bettergov.org\).

The next thing you should do is create and attach a static IP address. Without a static IP, the instance will create a new public IP address each time it's restarted. The static IP is a lasting reference that can reassigned to other instances.

Go to the Networking tab of the instance and follow the instruction to create and attach a static IP.

Update your DNS records to point to this IP address.

You should be able to connect to it easily using the SSH key you created. Here are two methods of connecting:

**All from the command line:**

```bash
$ ssh -i ~/.ssh/YOUR_PRIVATE_KEY.pem ubuntu@STATIC_IP_ADDRESS
```

**Using .ssh/config:**

```text
# .ssh/config
Host chartwerk.bettergov.org
    HostName STATIC_IP_ADDRESS
    User ubuntu
    IdentityFile ~/.ssh/YOUR_PRIVATE_KEY.pem
```

```bash
$ ssh chartwerk.bettergov.org
```

### Install prerequisites

Once connected to your instance, install some software in order to start the deployment process. Start by updating the system and installing pip and git.

```text
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install python3-pip python-dev python3-venv
$ sudo pip3 install --upgrade pip
$ sudo apt-get install git
```

{% hint style="info" %}
**Optional:** Sometimes it can be useful to edit files directly in Visual Studio Code. Follow the instructions [here](https://codeyarns.com/2018/05/03/how-to-edit-remote-files-using-remote-vscode/) to set that up.
{% endhint %}

### Set up a new Linux user

Even though Django has a pretty good [security track record](http://django.readthedocs.org/en/latest/releases/security.html), web applications can become compromised. If the application has limited access to resources on your server, potential damage can also be limited. Your web applications should run as system users with limited privileges.

Create your own user login.

```bash
# opens interactive useradd
$ sudo adduser <username>
$ sudo usermod -aG sudo <username>
```

As your user, create or copy a GitHub SSH key into `/home/YOU/.ssh/`. Then test your connection to GitHub, configure your information and clone into the project directory.

```bash
$ sudo su - <username>
# Create or copy GitHub SSH into ~/.ssh/id_rsa, for example
# Then restrict the permissions
$ chmod 600 ~/.ssh/id_rsa
$ git config --global user.name "Firstname Lastname"
$ git config --global user.email me@example.com
```

### Pull the Django app from Git

While logged in as the user you created credentials for, clone the git project.

```bash
$ git clone git@github.com:bettergov/bga_chartwerk.git ~/chartwerk.bettergov.org/bga_chartwerk
```

Then create a virtual environment and install the Python dependencies from `requirements.txt`.

```bash
$ cd ~/chartwerk.bettergov.org
$ python3 -m venv .
$ source chartwerk-env/bin/activate
(chartwerk-env) $ pip install -r bga_chartwerk/requirements.txt
(chartwerk-env) $ pip install --upgrade pip
(chartwerk-env) $ deactivate
```

### Set up PostgreSQL

Install the necessary postgres packages.

```bash
$ sudo apt-get install libpq-dev postgresql postgresql-contrib
```

Now let's create the production database and user. Inside the terminal, type:

```bash
$ sudo su - postgres
postgres@ip-xxx:~$ createuser --interactive -P
Enter name of role to add: chartwerk
Enter password for new role:
Enter it again:
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) n
Shall the new role be allowed to create more new roles? (y/n) n
postgres@ip-xxx:~$ createdb --owner chartwerk bga_chartwerk
postgres@ip-xxx:~$ logout
```

Now you should be ready to run migrations commands on the new DB.

```bash
(chartwerk-env) $ cd ~/chartwerk.bettergov.org/bga_chartwerk
(chartwerk-env) $ ./manage.py migrate
```

Don't forget to create a superuser by typing:

```bash
(chartwerk-env) $ ./manage.py createsuperuser
```

### Interlude: Sanity check

At this point, you can try launching your Django app on the built-in Django server.

```bash
(chartwerk-env) $ ./manage.py runserver 0.0.0.0:8000
```

You should be able to view the Django app at `STATIC_IP:8000`. Make sure the IP is whitelisted in the ALLOWED\_HOSTS of your Django settings file. Make sure the port 8000 is allowed in your Lightsail settings \(Networking &gt; Firewall\).

When you're finished testing, hit `Ctrl+C` to stop the django webserver from running.

### Configure Gunicorn with Supervisor

Now that the application is set up properly, it's time to configure our gateway for sending requests to our Django application. We will use [Gunicorn](http://gunicorn.org/) as our gateway.

Check that gunicorn can run without a hitch:

```bash
# Note that the port is different, to confirm we're not viewing a cached version
(chartwerk-env) $ gunicorn --bind 0.0.0.0:8001 bga_chartwerk.wsgi:application
```

You'll notice that the static files \(CSS, JS\) aren't loading — these will be handled separately.

When you're finished testing, hit `Ctrl+C` to stop gunicorn from running.

Now it's time to operate gunicorn from a service to make sure it's running continuously. Rather than setting up a `systemd` service, we will use a more robust way with [Supervisor](http://supervisord.org/).

First we'll create our startup script:

```bash
#!/bin/bash

NAME="bga_chartwerk"                                               # Name of the application
DJANGODIR=/home/patrick/chartwerk.bettergov.org/bga_chartwerk      # Django project directory
VIRTUALENV=/home/patrick/chartwerk.bettergov.org                   # virtual env directory
SOCKFILE=/home/patrick/chartwerk.bettergov.org/run/gunicorn.sock   # we will communicate using this unix socket
NUM_WORKERS=3                                                      # how many worker processes should Gunicorn spawn
DJANGO_SETTINGS_MODULE=bga_chartwerk.settings                      # which settings file should Django use
DJANGO_WSGI_MODULE=bga_chartwerk.wsgi                              # WSGI module name
USER=patrick

echo "Starting $NAME as `whoami`"

# Activate the virtual environment
cd $DJANGODIR
source $VIRTUALENV/bin/activate
export DJANGO_SETTINGS_MODULE=$DJANGO_SETTINGS_MODULE
export PYTHONPATH=$DJANGODIR:$PYTHONPATH

# Create the run directory if it doesn't exist
RUNDIR=$(dirname $SOCKFILE)
test -d $RUNDIR || mkdir -p $RUNDIR

# Start your Django Unicorn
# Programs meant to be run under supervisor should not daemonize themselves (do not use --daemon)
exec $VIRTUALENV/bin/gunicorn $DJANGO_WSGI_MODULE:application \
        --name $NAME \
        --workers $NUM_WORKERS \
        --user=$USER \
        --bind=unix:$SOCKFILE \
        --log-level=debug \
        --log-file=-
```

To install supervisor, type outside of the virtual environment:

```bash
$ sudo apt-get install supervisor
```

Then add a new `.conf` file to monitor gunicorn:

```bash
$ sudo vim /etc/supervisor/conf.d/gunicorn.conf
```

and add into the file:

```text
[program:chartwerk_guni]
directory=/home/patrick/chartwerk.bettergov.org/bga_chartwerk
command=/home/patrick/chartwerk.bettergov.org/bin/gunicorn_start
autostart=true
autorestart=true
stderr_logfile=/home/patrick/chartwerk.bettergov.org/logs/gunicorn.err.log
stdout_logfile=/home/patrick/chartwerk.bettergov.org/logs/gunicorn.out.log
user=patrick
environment=LANG=en_US.UTF-8,LC_ALL=en_US.UTF-8
```

Create the directory to store your application's log messages:

```bash
$ mkdir -p ~/chartwerk.bettergov.org/logs/
```

After you save the configuration file for your program you can ask supervisor to reread config files and update \(which will start the newly registered app\).

```bash
$ sudo supervisorctl reread
$ sudo supervisorctl update
```

You can also check the status of our app or start, stop or restart it using supervisor.

```bash
$ sudo supervisorctl status chartwerk_guni
$ sudo supervisorctl stop chartwerk_guni
$ sudo supervisorctl start chartwerk_guni
$ sudo supervisorctl restart chartwerk_guni
```

### Configure Nginx

Now we set up [Nginx](https://www.nginx.com/) to send traffic to Gunicorn. First we have to install Nginx:

```bash
$ sudo apt-get install nginx
```

Then create a new configuration file `/etc/nginx/sites-available/chartwerk` with the following contents:

```bash
server {
    listen 80;
    server_name SERVER_DOMAIN_OR_IP;
    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        alias /home/patrick/chartwerk.bettergov.org/static/;
    }
    location / {
        include proxy_params;
        proxy_pass http://unix:/home/patrick/chartwerk.bettergov.org/run/gunicorn.sock;
    }
}
```

Initiate a link with:

```bash
$ sudo ln -s /etc/nginx/sites-available/chartwerk /etc/nginx/sites-enabled
```

Check Nginx configuration by running:

```bash
$ sudo service nginx status
```

Assuming all good, restart Nginx by running:

```bash
$ sudo service nginx restart
```

### Deployment checklist

[https://docs.djangoproject.com/en/2.0/howto/deployment/checklist/](https://docs.djangoproject.com/en/2.0/howto/deployment/checklist/)

You shouldn't store sensitive information directly in your Django settings. Store the information as environment variables that you reference in your settings.

In settings.py:

```python
SECRET_KEY = os.environ['SECRET_KEY']
```

In bin/gunicorn\_start, append to the gunicorn command:

```bash
exec $VIRTUALENV/bin/gunicorn $DJANGO_WSGI_MODULE:application \
    ........    
    -e SECRET_KEY='abcabcabc...' \
    ........
```

### Set up SSL

Follow the [Certbot instructions](https://certbot.eff.org/lets-encrypt/ubuntuxenial-nginx). Note that this requires the domain to be set up already.

