# Setting up your Mac

[NPR Visuals](http://blog.apps.npr.org/2013/06/06/how-to-setup-a-developers-environment.html) and [The New York Times Interactive News](https://open.nytimes.com/set-up-your-mac-like-an-interactive-news-developer-bb8d2c4097e5) have helpful guides for setting up a Mac for development. Largely pulling from those guides, here are the essentials for setting up your Mac for developing apps and graphics.

## Prerequisites

### Update your software

Go to the 'Managed Software Center' and make sure your computer is set up with the latest operational and security updates.

### Install command line tools

Open your 'Terminal' app and run the following to install command line tools:

```text
xcode-select --install
```

If it doesn't install, consult the [NPR Visuals guide](http://blog.apps.npr.org/2013/06/06/how-to-setup-a-developers-environment.html#install-command-line-tools) for alternate install instructions.

## Terminal and text editor

[iTerm2](https://www.iterm2.com/) is awesome. Unless you have a compelling argument against it, use it in place of the default terminal.

For a text editor, I use [Visual Studio Code](https://code.visualstudio.com/) \(built-in git tooling and terminal, large plugin ecosystem\). Other popular alternatives are [Atom](https://atom.io/), [Sublime Text](https://www.sublimetext.com/), and vim.

## Homebrew

[Homebrew](http://brew.sh/) is a package manager for command line utilities — it'll help you easily install the other requirements. To install it, open your terminal and run:

```text
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Follow the instructions in the prompt. You'll be all set with Homebrew when you see:

```text
“Your system is ready to brew.”
```

## Credentials and keys

### Amazon Web Services

We use Amazon Web Services to host most of our scripts and applications.

First, you'll need access credentials for AWS. From the root user account \(digital@bettergov.org\), create a new IAM user \(e.g. jdoe@bettergov.org\) and create an access key for that user. Note the access key and secret access key.

Make a directory called .aws and change into it.

```text
mkdir ~/.aws
cd ~/.aws
```

Create two files, config and credentials.

```text
touch config credentials
```

In config, paste your AWS region.

```text
[default]
region = us-east-2
```

In credentials, paste your access key and secret key.

```text
[default]
aws_access_key_id = xxxxxxxxxxxxxxxxxxxx
aws_secret_access_key = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Google Cloud

Through your work email, you should have access to the [Google Cloud Platform](https://console.cloud.google.com). Both our graphics rig and projects rig programmatically access Google Drive/Docs/Sheets; that access is managed from Google Cloud Platform.

Instructions for setting up Google Cloud authorization for those projects are included in each project's documentation.

### GitHub

If you aren't already on GitHub, create an account.

Once you’ve created a GitHub account, you’ll need to give your machine access to pull and push to repositories. [GitHub’s documentation](https://help.github.com/articles/connecting-to-github-with-ssh/) will help you through setting up your SSH keys.

You'll also want to configure the default identity for your git commits.

```text
git config --global user.email "$YOUR_EMAIL@bettergov.org"
git config --global user.name "$YOUR_NAME"
```

Finally, you should join the [@bettergov](https://github.com/bettergov/) organization. From the bga-admin account \(digital@bettergov.org\), invite your account as an owner.

## Python

You may end up using Python for offline analyses, old Flask apps, or a Django app. Rule \#1 for Python development is to avoid the system-provided Python.

I recommend following Jacob Kaplan-Moss's guide for setting up a Python development environment, using pyenv, pipenv and pipsi.

{% embed url="https://jacobian.org/2018/feb/21/python-environment-2018/" %}

## Node

Install Node using [nvm](https://github.com/nvm-sh/nvm). This will allow you to easily install and switch between multiple versions of Node as needed.

```text
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

See [nvm's docs](https://github.com/nvm-sh/nvm#usage) for instructions on usage.

## Other software

Outside of the command line, here are other programs that may help your work:

| Application | Usage |
| :--- | :--- |
| Adobe Illustrator | Draw graphics, particularly in combination with [ai2html](http://ai2html.org/) |
| Adobe Photoshop | Edit photos |
| Cyberduck | A point-and-click file explorer for SFTP connections and S3 buckets |
| Docker | Manage and run containerized applications |
| MySQLWorkbench | MySQL database GUI |
| pgAdmin | Postgres database GUI |
| QGIS | Draw maps |

