# Dockerized Drupal development with Lando

For a stint in time I was using Lando for local development of our Drupal CMS. The required resources ended up draining more resources than was performant on my MacBook, so I switched development strategies. Your mileage may vary.

## Developing locally with Lando

Lando lets us easily spin up a local development environment. They have a configuration specifically for syncing with Pantheon.

### Requirements

1. Install [Docker for Mac](https://docs.docker.com/docker-for-mac/install/) from `Docker.dmg`from the Docker site
2. Ensure [Homebrew](https://brew.sh/) is installed and up-to-date
3. Install [Lando](https://github.com/lando/lando) using Homebrew: `brew install cask lando`

### Getting started

1. Create an empty directory
2. `cd` into the new directory
3. Run `lando init pantheon`. You should be able to select your Pantheon identity and site.
4. From Pantheon, [create a machine token](https://pantheon.io/docs/machine-tokens/). At the end, run the following to add the token to lando: `lando terminus auth:login --machine-token=<machine-token>`
5. Run `lando start`. This spins up the local app server.
6. Run `lando pull`. You will be able to pull code, database and files from a Pantheon environment. 

### More about lando pull

Generally speaking, we use `lando pull` for the initial build.

For image syncing, rsync them: `lando pull --rsync`

More examples, from Lando's docs:

```bash
# Pull the latest code, database and files
# This will pull the environment associated with your currently checked out git branch
lando pull

# Skip a code merge
lando pull --code=none

# Pull only the database from the test environment
lando pull --database=test --files=none

# Pull only the files
lando pull --database=none

# Pull only the latest files without grabbing a files backup
lando pull --database=none --rsync
```

### Pushing local changes

We use`lando push`to push up any changes to code, database and files made locally.

Lando only pushes up code by default.

```bash
# Push the local code
# This will push to the environment associated with your currently checked out git branch
lando push

# Push only the database and code
lando push --files=none

 # Push only the files and code
 lando push --database=none 
```

### Shutting down lando

```bash
# Force shutdown all Docker containers
docker container kill $(docker container ls -a -q)

# Remove all Docker containers
docker container rm $(docker container ls -a -q)

# Remove all Docker images
docker image rm $(docker image ls -a -q)
```

