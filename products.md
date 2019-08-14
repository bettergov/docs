---
description: Listing BGA's deployed sites and tools.
---

# Product list

## Overview

| Name | Deployed on... | GitHub repo |
| :--- | :--- | :--- |
| www.bettergov.org | Pantheon | [bettergov/bettergov.org](https://github.com/bettergov/bettergov.org) |
| Project pages | S3 | n/a |
| Misc. assets | S3 | n/a |
| Graphics | S3 | [bettergov/graphics-archive](https://github.com/bettergov/graphics-archive) |
| salary.bettergov.org | n/a | [datamade/bga-payroll](https://github.com/datamade/bga-payroll) |
| pensions.bettergov.org | n/a | [datamade/bga-pensions](https://github.com/datamade/bga-pensions) |
| votersguide.bettergov.org | S3 | [bettergov/app-voterguide](https://github.com/bettergov/app-voterguide) |
| ilvotes.org | S3 | [bettergov/ilvotes.org](https://github.com/bettergov/ilvotes.org) |
| chi.vote | EC2/S3 | [chi-vote/chivote](https://github.com/chi-vote/chivote) |
| membership.bettergov.org | Netlify | [bettergov/membership-bettergov-org](https://github.com/bettergov/membership-bettergov-org) |
| pixel.bettergov.org | Lightsail | n/a |
| FOIAmail | EC2 | [bettergov/foiamail](https://github.com/bettergov/foiamail) |

{% hint style="info" %}
To add a new subdomain, contact our IT consultants.
{% endhint %}

## www.bettergov.org

See CMS &gt; For developers for full build and deployment details.

{% page-ref page="cms/for-developers.md" %}

## Project pages

* Production: projects.bettergov.org \(S3\)
* Staging: stage-projects.bettergov.org \(S3\)
* Asset: media.bettergov.org \(S3\)

See Project pages for how to build and deploy to these buckets.

{% page-ref page="project-pages.md" %}

Other deployment notes...

### Password-protected staging bucket

stage-projects.bettergov.org is password-protected via [s3auth.com](http://www.s3auth.com/). Here's language from their site:

> s3auth.com is a [Basic HTTP Auth](http://en.wikipedia.org/wiki/Basic_access_authentication) gateway in front of your private [Amazon S3](http://aws.amazon.com/s3/) bucket.
>
> Point your test.example.com CNAME to relay.s3auth.com, and register the domain here. You will be able to access bucket's content in a browser with HTTP basic auth. Your bucket will be accessible using your [Amazon IAM](http://aws.amazon.com/iam/) credentials and with custom user/password pairs in your .htpasswd file \(similar to Apache HTTP Server\).
>
> For example, try http://maven.s3auth.com/ \(with username s3auth and password s3auth\). You will access content of Amazon S3 bucket maven.s3auth.com, which is not readable anonymously otherwise.

Configuration for s3auth.com is managed through the bga-admin GitHub account.

## Miscellaneous assets

* media.bettergov.org

Favicons, Fontello icons, previous project assets

## Graphics

* graphics.bettergov.org

See Embeddable graphics for how these are built and deployed.

{% page-ref page="embeddable-graphics.md" %}

## Databases

* salary.bettergov.org \([GitHub](https://github.com/datamade/bga-payroll)\)
* pensions.bettergov.org \([GitHub](https://github.com/datamade/bga-pensions)\)

Development and hosting of these are managed by [DataMade](https://datamade.us/).

## Voter guides

* votersguide.bettergov.org \([GitHub](https://github.com/bettergov/app-voterguide)\)
* ilvotes.org \([GitHub](https://github.com/bettergov/ilvotes.org)\)
* chi.vote \([GitHub](https://github.com/chi-vote)\)

### votersguide.bettergov.org

Built for the 2018 primary election. Was a Django app, now it's just a Cloudfront endpoint and an S3 subdirectory.

### ilvotes.org

Built for the 2018 general election.

### chi.vote

Built for the 2019 Chicago municipal election. The supporting infrastructure is owned by [The Chicago Reporter](https://www.chicagoreporter.com/).

## membership.bettergov.org

* [Live link](https://membership.bettergov.org/)
* [GitHub](https://github.com/bettergov/membership-bettergov-org)
* [Netlify dashboard](https://app.netlify.com/sites/festive-heisenberg-527df5/overview)
* [Netlify CMS admin](https://membership.bettergov.org/admin/)

## pixel.bettergov.org

Related repos:

* [bettergov/pixel](https://github.com/bettergov/pixel) — The tracking code \([live link](https://pixel.bettergov.org/pixel.js)\)
* [bettergov/pixel-ping](https://github.com/bettergov/pixel-ping) — The tracker
* [bettergov/pixel-pong](https://github.com/bettergov/pixel-pong) — The dashboard \([live link](http://pixel.bettergov.org:3000/)\)

Deployment: [AWS Lightsail](https://lightsail.aws.amazon.com/ls/webapp/us-east-2/instances/pixel.bettergov.org/connect)

_**TODO: Organize these into a cohesive project.**_

## FOIAMail

* [GitHub](https://github.com/bettergov/foiamail)

Deployed on EC2 sporadically.

_**TODO: Upgrade to python3 + versioning**_

