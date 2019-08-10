---
description: 'D3-driven graphics, made responsive and embeddable using pym.js.'
---

# Embeddable graphics

## Building graphics

I've built and deployed most of our graphics using the NPR Daily Graphics rig — in its original form,[`nprapps/dailygraphics`](https://github.com/nprapps/dailygraphics), and now its latest incarnation, [`nprapps/dailygraphics-next`](https://github.com/nprapps/dailygraphics-next) \(dg-next\). The setup combines Google Sheets data management, D3 graphics templates and S3 deployment into a single workflow to easily create graphics that are embedded into stories.

Running dg-next requires a few repositories — a rig repo, a template repo, and your graphics repo — as well as some configuration for fetching data from Google Sheets and deploying to AWS S3.

### Getting started

To simplify getting started, I've packed all of the setup for dg-next into a single repository: [`bettergov/workspace-dailygraphics-next`](https://github.com/bettergov/workspace-dailygraphics-next).

Assuming you have Yarn and iTerm on your machine, getting your dg-next rig started from scratch is really trivial:

```bash
git clone https://github.com/bettergov/workspace-dailygraphics-next.git dgnext
cd dgnext
# [] manually check config.json
# [] manually check .gitmodules
yarn install
# [] manually fill out .env with google and aws credentials
yarn start:iterm
```

For more detailed explanation, see the [workspace-dailygraphics-next](https://github.com/bettergov/workspace-dailygraphics-next) repo.

## Embedding graphics

pym.js

{% hint style="info" %}
Mobile Safari likes to give us grief with pym embeds, often appearing too big for its current frame. To get around this, add these attributes in the parent css:

```css
iframe {
    width: 1px;
    min-width: 100%;
}
```
{% endhint %}



