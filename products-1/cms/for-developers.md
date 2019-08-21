# For developers

## Hosting

Our site is hosted on [Pantheon](https://pantheon.io/docs/guides/quickstart/). From a development perspective, this gives us full access to our site's codebase and allows us to easily add collaborators from supporting organizations.

The most beneficial advantages of Pantheon involve versioning and workflows, including:

* Separate Dev, Test and Live environments which you can easily sync code, data and files between
* On-server development via SFTP Connection Mode
* Git version control
* Multidev environments — branches that live on their own urls
* [Terminus](https://pantheon.io/docs/terminus/), a command line interface for interacting with Pantheon

To get the lay of the Pantheon land, I recommend reading through their [Quick Start guide](https://pantheon.io/docs/guides/quickstart/). Here's their [full documentation](https://pantheon.io/docs/).

We also keep a version of our website on GitHub at [bettergov/bettergov.org](https://github.com/bettergov/bettergov.org). Be sure to follow the steps in [Syncing with GitHub](for-developers.md#syncing-with-github) to keep this version in sync with Pantheon.

## Local development

Pantheon outlines some options for local development in their docs.

{% embed url="https://pantheon.io/docs/local-development" %}

I use a homespun combination of SFTP mode, BrowserSync and Visual Studio Code:

{% page-ref page="../../tip-sheets/remote-local-dev-with-sftp-browsersync-and-vscode.md" %}

For an alternate local development strategy, check out this tip sheet:

{% page-ref page="../../tip-sheets/dockerized-drupal-development-with-lando.md" %}

## Using Terminus and \`pt\`

Terminus is a command-line interface for Pantheon; it's terrific for managing our website deployment, and it opens up access to certain commands that aren't exposed in the GUI \(i.e. `terminus multidev:create`\).

The syntax for terminus is a bit verbose, so I created a shorthand function `pt` to cut down on the typing. For example, if you're on the branch `new-feature`, you could run:

`terminus drush better-government-association.new-feature -- cc all`

Using our function:

`pt drush -- cc all`

To enable this function, add the following to your `~/.bash_profile`.

```bash
# Pantheon Terminus command based on current git branch
#
# For example, in the branch `feature-1`: `pt drush -- cc all`
# >> terminus drush better-government-association.feature-1 -- cc all
function pt() {
  # Get command (first arg)
  COMMAND_VAR=$1;

  # Get current git branch
  PANTHEON_ENV=$(git rev-parse --abbrev-ref HEAD);

  # Map master git branch to dev pantheon extension
  if [ $PANTHEON_ENV == "master" ]; then
    PANTHEON_ENV="dev"
  fi

  # Pop the first arg off the list of space-separated args ($*)
  shift;

  # Run final command
  terminus $COMMAND_VAR better-government-association.$PANTHEON_ENV $*
}
```

{% hint style="info" %}
This command is also available in the projects local .bashrc. You can access the command by running `source .bashrc` from the project root.
{% endhint %}

## Syncing with GitHub

We also keep a version of our website on GitHub at [bettergov/bettergov.org](https://github.com/bettergov/bettergov.org).

The simplest way to keep this version in sync with Pantheon is to edit`.git/config`and add GitHub to the origin branch.

```text
[remote "origin"]
    url = ssh://codeserver.dev.<SITE_UUID>@codeserver.dev.<SITE_UUID>.drush.in:2222/~/repository.git
    url = git@github.com:bettergov/bettergov.org.git
```

{% hint style="info" %}
Pantheon has a whole page of documentation on using GitHub's collaborative development tools with Pantheon. Feel free to [read through their docs](https://pantheon.io/docs/guides/collaborative-development/).
{% endhint %}

{% hint style="info" %}
If you use the project Makefile for set-up as described in the project's README.md, then git configuration will already be taken care of.
{% endhint %}

## Drupal site structure

Breaking down our site's page types and how they're built.

| Page type | How it's built | Example |
| :--- | :--- | :--- |
| Homepage | Custom [**Panels**](https://www.drupal.org/project/panels) page | [https://www.bettergov.org/](https://www.bettergov.org/) |
| Article | **Panels** node\_view | [https://www.bettergov.org/news/spiral-of-decline-heavy-burden-for-homeowners/](https://www.bettergov.org/news/spiral-of-decline-heavy-burden-for-homeowners/) |
| Backstory | **Panels** node\_view | [https://www.bettergov.org/backstory/chicago-city-council/](https://www.bettergov.org/backstory/chicago-city-council/) |
| Republish | **Panels** node\_view | [https://www.bettergov.org/node/1401909/republish/](https://www.bettergov.org/node/1401909/republish/) |
| Taxonomy term | **Panels** term\_view | [https://www.bettergov.org/newsroom/](https://www.bettergov.org/newsroom/) |
| Events list | \*\*\*\*[**Views**](https://www.drupal.org/project/views) view | [https://www.bettergov.org/events/](https://www.bettergov.org/events/) |
| Events archive list | **Views** view | [https://www.bettergov.org/events/archive/](https://www.bettergov.org/events/archive) |
| Event | Node template | [https://www.bettergov.org/events/watchdog-training-city-bureau/](https://www.bettergov.org/events/watchdog-training-city-bureau/) |
| Page w/ menu | Node template | [https://www.bettergov.org/about-us/](https://www.bettergov.org/about-us/) |
| Staff list | **Views** view | [https://www.bettergov.org/team/](https://www.bettergov.org/team/) |
| Staff member page | Node template | [https://www.bettergov.org/team/david-greising/](https://www.bettergov.org/team/david-greising/) |

## Cron

We're managing our Cron tasks via the Ultimate Cron module. To configure, go to [admin/config/system/cron](https://www.bettergov.org/admin/config/system/cron/).

We execute cron tasks as scheduled within Ultimate Cron, pinging Drupal's cron endpoint every minute. 

To do this, I've set up a cron job at [cron-job.org](//cron-job.org) using the digital@bettergov.org login. It pings our site's cron url, as defined in [admin/reports/status](https://www.bettergov.org/admin/reports/status/), once a minute.

We've disabled Poorman's Cron.

## Google Analytics and Google Tag Manager

Our [Google Analytics](https://analytics.google.com/) setup could be a whole wiki unto itself. Here, I'll lay out just the few integration details that impact our site. Keep these events and markup in mind as you develop our website.

### Analytics Drupal feature

Configuration and code specific to our analytics setup are captured in the Drupal 7 feature [BGA Analytics](https://www.bettergov.org/admin/structure/features/bga_analytics/).

### Loading Google Analytics via Google Tag Manager

Google Analytics is loaded on each page via [Google Tag Manager](https://tagmanager.google.com). We use two modules for this:

* [`google_tag`](https://www.drupal.org/project/google_tag/): Load Google Tag Manager
* [`datalayer`](https://www.drupal.org/project/datalayer/): Expose certain Drupal variables to Google Tag Manager, used for filtering and as custom analytics dimensions

### Custom conversion events

For some pages, we attach an event listener to an element that triggers a tag in Google Tag Manager, which triggers a conversion in Google Analytics.

First, we expose the page's nid to the client in a theme hook.

{% code-tabs %}
{% code-tabs-item title="sites/all/themes/orbit/template.php" %}
```php
/**
 * Implements hook_preprocess_page().
 */
function orbit_preprocess_page(&$vars) {
    ...
    
    // Add nid to js Drupal.settings if page is a node
    if ($node = menu_get_object()) {
        drupal_add_js(array('nid' => $node->nid),'setting');
    }
}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then we add event listeners based on the nid.

{% code-tabs %}
{% code-tabs-item title="sites/all/themes/orbit/resources/js/src/theme.js" %}
```javascript
import $ from 'jquery';
import ConversionEvents from './components/conversion-events';

Drupal.behaviors.bga = {
    attach: function(context, settings) {
        $(document).ready(function() {
            // Initalize conversion events
            ConversionEvents.init(settings.nid);
        });
    }
};
        
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="sites/all/themes/orbit/resources/js/src/components/conversion-events.js" %}
```javascript
export default {
  init: function(nid) {
    switch (Number(nid)) {
      // sign up page
      case 367:
        context
          .querySelector('.field-body')
          .addEventListener('click', function(e) {
            e.target.matches('button[type="submit"]') &&
              dataLayer.push({ event: 'email-signup-submit' });
          });
        break;

      // sign up popup
      case 1400929:
        context
          .querySelector('.field-body')
          .addEventListener('click', function(e) {
            if (e.target.matches('button[type="submit"]')) {
              dataLayer.push({ event: 'email-popup-submit' });

              setCookie('SESSpopupsearchlockoutbypass', '1', 3000);
              setTimeout(function() {
                parent.location.reload();
              }, 3000);
            }
          });
        break;

      // donate page
      case 1401690:
        context
          .querySelector('.field-body')
          .addEventListener('click', function(e) {
            e.target.matches('button[type="submit"]') &&
              dataLayer.push({ event: 'donation-submit' });
          });
        break;
    }
  }
};

function setCookie(cname, cvalue, exdays) {
  var d = new Date();
  d.setTime(d.getTime() + exdays * 24 * 60 * 60 * 1000);
  var expires = 'expires=' + d.toUTCString();
  document.cookie = cname + '=' + cvalue + ';' + expires + ';path=/';
}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Editing the Data Layer

We can easily expose article data to Google Tag Manager by initializing the Data Layer with some default values. That data can be passed up to Google Analytics as custom dimensions, or used for logic in Google Tag Manager.

We generally keep the defaults; additional data layer logic happens in `sites/all/modules/custom/features/bga_analytics/bga_analytics.module`.

The implication of using the Data Layer this way is that tags that use it are brittle to changes. Changes to the website should be cognizant of effects on Google Tag Manager triggers and tags.

#### Example use: Custom dimensions

See the tag [GA Settings](https://tagmanager.google.com/#/container/accounts/4701561889/containers/11462756/workspaces/32/variables).

#### Example use: Functional logic

See the tag [GA Event Trigger - Article Scroll Depth](https://tagmanager.google.com/#/container/accounts/4701561889/containers/11462756/workspaces/32/variables).

#### 



