# Remote/local dev with SFTP, BrowserSync and VSCode

When developing our website, I prefer to work via SFTP mode; the database and server are still hosted remotely, but the code is edited locally and certain front-end assets are loaded on the client, allowing for faster loading and automatic reloads.

## Quick setup

```bash
git clone https://github.com/bettergov/bettergov.org.git
source .bashrc
make

# In a separate terminal, configure SFTP mode.
make sftp
```

## In detail

01. Read Pantheon's docs on [Developing on Pantheon Directly with SFTP Mode](https://pantheon.io/docs/sftp)

02. Make sure your Pantheon environment is set to SFTP mode: `terminus connection:set better-government-association.<PANTHEON_ENV> sftp`

03. Install the [SFTP extension](https://marketplace.visualstudio.com/items?itemName=liximomo.sftp) for Visual Studio Code.

04. Configure the SFTP connection by creating `.vscode/sftp.json`:

```javascript
[
  {
    "name": "code",
    "context": ".",
    "protocol": "sftp",
    "host": "appserver.dev.67695a44-d32a-443d-a212-a28ddfbee02b.drush.in",
    "port": 2222,
    "username": "dev.67695a44-d32a-443d-a212-a28ddfbee02b",
    "privateKeyPath": "~/.ssh/id_rsa",
    "remotePath": "./code/",
    "uploadOnSave": false,
    "ignoreFile": ".gitignore",
    "watcher": {
      "files": "sites/all/**/*",
      "autoUpload": true,
      "autoDelete": true
    }
  }
]
```

05. Create a git hook `post-checkout` to update `sftp.json` whenever we check out a new git branch.

{% code-tabs %}
{% code-tabs-item title=".git/hooks/post-checkout" %}
```bash
#!/bin/bash

set -e

# printf '\npost-checkout hook\n\n'

prevHEAD=$1
newHEAD=$2
checkoutType=$3

PANTHEON_ENV=$(git rev-parse --abbrev-ref HEAD)

# Map master git branch to dev pantheon extension
if [ $PANTHEON_ENV == "master" ]; then
  PANTHEON_ENV="dev"
fi

[[ $checkoutType == 1 ]] && checkoutType='branch' ||
                            checkoutType='file' ;

# Create SFTP config
cat >./.vscode/sftp.json <<EOF
[
  {
    "name": "code",
    "context": ".",
    "protocol": "sftp",
    "host": "appserver.$PANTHEON_ENV.67695a44-d32a-443d-a212-a28ddfbee02b.drush.in",
    "port": 2222,
    "username": "$PANTHEON_ENV.67695a44-d32a-443d-a212-a28ddfbee02b",
    "privateKeyPath": "~/.ssh/id_rsa",
    "remotePath": "./code/",
    "uploadOnSave": false,
    "ignoreFile": ".gitignore",
    "watcher": {
      "files": "sites/all/**/*",
      "autoUpload": false,
      "autoDelete": false
    }
  }
]
EOF

# Print Pantheon information
printf '\nOn Pantheon env '
echo \'$PANTHEON_ENV\'
printf '\n'
terminus env:info better-government-association.$PANTHEON_ENV

```
{% endcode-tabs-item %}
{% endcode-tabs %}

06. Note our BrowserSync configuration.

{% code-tabs %}
{% code-tabs-item title="sites/all/themes/orbit/gulpfile.babel.js" %}
```javascript
const gulp = require("gulp");
const browserSync = require("browser-sync").create();

/**
 * Basic example of compiling scss into css.
 * Note browserSync.stream at the end.
 */
const compileThemeStyle = function(done) {
    gulp
        .src("resources/scss/*.scss")
        .pipe(cssTasks())
        .pipe(gulp.dest("resources/css"))
        .pipe(browserSync.stream());
    
    done();
};

/**
 * @task serve
 * Launch the server via BrowserSync
 */
export const startServer = function(done) {
  // Get the current Pantheon environment from the current git branch
  let PANTHEON_ENV = execSync("git rev-parse --abbrev-ref HEAD", {
    encoding: "utf-8"
  }).trim();

  if (PANTHEON_ENV == "master") PANTHEON_ENV = "dev";

  browserSync.init({
    // Change as required
    proxy: `https://${PANTHEON_ENV}-better-government-association.pantheonsite.io/`,
    open: false,
    socket: {
      // For local development only use the default Browsersync local URL.
      domain: "localhost:3000"
      // For external development (e.g on a mobile or tablet) use an external URL.
      // You will need to update this to whatever BS tells you is the external URL when you run Gulp.
      // domain: '10.0.71.209:3000'
    },
    // Serve local theme files at `local-theme` route...
    serveStatic: [
      {
        route: "/local-theme",
        dir: "."
      }
    ],
    // ... and then rewrite the remote theme filepaths to reference local files instead.
    rewriteRules: [
      {
        match: /sites\/all\/themes\/orbit\//g,
        fn: () => "local-theme/"
      }
    ]
  });

  done();
};

const serve = gulp.series(compileStyle, startServer);
```
{% endcode-tabs-item %}
{% endcode-tabs %}

07. Launch the development server.

```bash
cd sites/all/themes/orbit && gulp
```

