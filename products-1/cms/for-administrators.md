# For administrators

## Accessing our test env

When making a significant configuration change, do it in the test env first and take notes along the way. This prevents unpredictable errors or edits in progress from showing up on our live site.

**To access the test env,** go to [http://test-better-government-association.pantheonsite.io/](http://test-better-government-association.pantheonsite.io/). If you're prompted for a password to see the site...

* Username: `orbit`
* Password: `ab-2012`

When you're comfortable with the changes you've made, replicate them manually in the live site.

## TODO: The homepage

## Administering authors

### Creating

To create a new author, navigate to Add Content &gt; Team Member \([direct link](https://www.bettergov.org/node/add/team-member/)\).

If the author is BGA staff:

* Fill in **Name**, **Job Title**, and applicable contact information \(usually **Email** and **Twitter**\)
* Paste their bio in the **Body** and upload a **Team Member Image**
* Finally select their **Team/Bio Group** — this will add them to our [staff page](https://www.bettergov.org/team)

If the author is a BGA intern:

* Fill in **Name**, **Job Title**, and applicable contact information

If the author works for an outside organization:

* Fill in **Name** and **Organization**

{% hint style="warning" %}
It's worth repeating: **Do not select a Team/Bio Group** unless the team member is BGA staff.
{% endhint %}

### Removing staff

To remove departing staff members:

* Change their **Job Title** to "Former xxxxxx"
* Set their **Team/Bio Group** to "- None -"
* Remove their **Email**

## Administering job listings

### Adding

1. [Add a new article.](https://www.bettergov.org/node/add/article/) Set the type to **Jobs**. Fill out the article and publish it.
2. Once that's published, edit the [Jobs & Internships page](https://www.bettergov.org/jobs-internships). Add the job to the body of the page in two places:

```markup
<h6 id="top">JUMP TO:</h6>
<!-- edit 1: add job to top list -->
<p><a href="#web-graphics-editor">WEB & GRAPHICS EDITOR</a></p>
<!-- end edit -->
<p><a href="#intern">INTERNSHIPS</a></p>

<!-- edit 2: add detailed job listing -->
<hr>
<h3 id="web-graphics-editor">WEB & GRAPHICS EDITOR</h3>
<p>We're seeking a Web & Graphics Editor to lead our digital publishing and design efforts.</p>
<a href="/news/bga-is-hiring-a-web-graphics-editor">Full job listing ➔</a>
<!-- end edit -->
```

### Removing

1. Take the job down from the [Jobs & Internships page](https://www.bettergov.org/jobs-internships).
2. Edit the job listing itself. At the top of the body, add a paragraph noting that the job listing is no longer open, e.g. "Applications for this position are no longer being considered."

## Administering CMS users

### Adding

To create a new CMS user:

* Navigate to Administration &gt; People &gt; Add user \([direct link](https://www.bettergov.org/admin/people/create/), administrators only\)
* Fill out the form
* At the bottom, check **Client Administrator** and **Notify user of new account**
* Save

### Blocking

To remove departing staff members:

* Edit the user \(select them from [this list](https://www.bettergov.org/admin/people/)\), select **Status** &gt; **Blocked**, and save

