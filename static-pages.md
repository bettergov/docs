# Static pages

Consult example: [https://github.com/DallasMorningNews/generator-dmninteractives/wiki](https://github.com/DallasMorningNews/generator-dmninteractives/wiki)

Checklist \(draft\):

* [ ] Analytics
* [ ] Staging urls redirect to production
* [ ] Printer friendly
* [ ] Performance checks
* [ ] Share button
* [ ] Loaded on bettergov.org
* [ ] Sent links + pdfs to editor
* [ ] Make public

### **Publishing our code**

#### **1. Delete sensitive information, including old drafts, from git history.** 

Following [GitHub's guidelines for removing sensitive information](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository), use the [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) to wipe sensitive information from commit history.

We should delete old story drafts, for example.

```bash
$ bfg --delete-files archie.json
```

#### **2. Set source documents to view only.**

Of course, we should keep links to our source material. But do it in a way that it can't be tampered with! Any source documents should be set to public view only.

#### **3. Archive.**

If the project is published, archive it in GitHub to indicate that it is no longer under development.

