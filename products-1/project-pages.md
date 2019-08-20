# Project pages

We host big stories and complex applications outside of our CMS — this makes the code easier to manage and grants us more flexibility than we can achieve inside our CMS.

For a primer on off-platform pages, Ben Welsh's [First News App tutorial](https://first-news-app.readthedocs.io/en/latest/) is excellent.

## Bootstrapping our project \(or not\)

Historically we've bootstrapped our projects a few different ways...

* [bettergov/template\_bga-project](https://github.com/bettergov/template_bga-project)
* [bettergov/generator-bga-interactives](https://github.com/bettergov/generator-bga-interactives) \(based on [The-Politico/generator-politico-interactives](https://github.com/The-Politico/generator-politico-interactives)\)
* [bettergov/app-template](https://github.com/bettergov/app-template) \(based on [nprapps/app-template](https://github.com/nprapps/app-template)\)

For the adept developer, try your hand at building template\_bga-project better or make something even better using a static site framework like Gatsby.

For someone who doesn't want to tangle with code, I'd recommend a couple options:

* Sign up with a service like [Shorthand](https://shorthand.com).
* Take advantage of our Feature layout in our CMS.

## Deploying our project

Drafts of projects should be deployed and tested on our password-protected staging S3 bucket, stage-projects.bettergov.org.

Published projects should be deployed and tested on our production S3 bucket, projects.bettergov.org.

Note that projects.bettergov.org is served through an AWS Cloudfront CDN, meaning that files will cache by default and changes won't necessarily show up immediately without triggering an invalidation.

## Publishing to our CMS

See CMS &gt; For editors &gt; [Adding an off-platform article](cms/for-editors.md#adding-an-off-platform-article).

## Publishing our project code

#### **0. Consolidate into a monorepo.**

Depending on the complexity of the project, you may have your files split across several projects, some of which are dependent on others. I've found it practical to consolidate everything into a single repo.

* **Merge projects and history:** [`shopsys/monorepo-tools`](https://github.com/shopsys/monorepo-tools) provides some handy scripts to merge these separate repos into a single repo while preserving their commit history. 
* **Manage dependencies with yarn workspaces:** [See Yarn's workspaces documentation to set up.](https://yarnpkg.com/lang/en/docs/workspaces/) ****You'll create a slim `package.json` in your root directory, and modify the `package.json` in the child directories based on project interdependencies.

#### 1. Delete sensitive information, including old drafts, from git history. 

Following [GitHub's guidelines for removing sensitive information](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository), use the [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) to wipe sensitive information from commit history.

Delete old story drafts, for example:

```bash
$ bfg --delete-files archie.json
```

#### 2. Set source documents to view only.

We should keep links to our source material in the public repo. But do it in a way that it can't be tampered with! Any source documents should be set to public view only.

#### 3. Archive.

If the project is published, archive it in GitHub to indicate that it is no longer under development.

