\*Disclaimer: This is a simplified version of a Git workflow. For more
detailed workflows take a look at [Gitflow: A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)

# Git workflow

The central repo holds two main branches with an infinite lifetime:

* master
* develop

We consider `master` to be the main branch where the source code of HEAD always
reflects a `PRODUCTION` state environment.

We consider `develop` to be the main branch where the source code of HEAD always
reflects a `STAGING` environment state with the latest delivered development changes
for the next release.

When the source code in the `develop` branch reaches a stable point and is ready
to be released, all of the changes should be merged back into `master`. Therefore,
each time when changes are merged back into `master`, this is a new `PRODUCTION`
release by definition.

## 📦 Feature branch

Create a local feature branch from develop for development.

````
git checkout develop
git pull origin develop
git checkout -b feature/my-feature-branch
*Note the "/feature" prefix.
````

Work in your feature branch and commit the changes.

````
git status
git add -A
git commit
````

Write a good commit message.

````
Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary.  Wrap it to about 72
characters or so.

Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
or "Fixes bug." This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

* Bullet points are okay, too
* Typically a hyphen or asterisk is used for the bullet, preceded by a
  single space, with blank lines in between, but conventions vary here
````

Prepend summary with issue number, if exists.

````
[#123] Capitalized, short (50 chars or less) summary
````

Share your feature branch.

````
git push origin feature/my-feature-branch
````

Open a Pull Request and ping project members for a code review.

#### Rebase frequently

**Rebase frequently** your own feature branch to incorporate changes in develop and to keep a clean history.

````
git checkout develop
git pull origin develop
git checkout [branch]
git rebase develop
<resolve conflicts>
git push -f # Notice --force commit as remote branch is different
````
 
Here's a brief video on how to rebase feature branches: https://www.useloom.com/share/0bcbf05c3d5446eea5bd406973c5c75c

A good commits log:

````
* Add the models and forms for potatoes
* Add API to interact with potatoes, along with unit tests
* Add the comment dialog for reviewing potatoes
````

An ugly commits log:

````
* Add the models and forms for potatoes
* Decided that the is_spud field wasn't necessary and removed it
* Forgot forms partial
* Add API to interact with potatoes, along with unit tests
* One of the tests failed, fixed it
* Added the comment dialog for reviewing potatoes
* Fixed a typo
* Another typo
````

#### Merge when ready

If everything's good, merge your branch to develop and push your changes.

````
git checkout develop
git merge [branch]
git push origin develop
````

Optional: Delete your local feature branch.

````
git branch -d [branch]
````

Optional: Delete your remote feature branch.

````
git push origin :[branch]
````

## 🔥 Hotfix branch

Make you branch out of `master`

```
git checkout -b hotifx/...
# make fixes
```

#### Merge when ready

```
git checkout master
git merge hotifx/...
git push origin master
```

Apply hotfix to `develop` (or release branch if present) as well:

```
git checkout hotifx/...
git rebase develop
git checkout develop
git merge hotifx/...
git push origin develop
```

### **Optional* Before release

In order to avoid `merge commits` in your history when 🔥hotfixes were made,  before release first rebase `master`:

```
git checkout master
git rebase develop
```

**Afterwards** you'll need to force push `master` at the **end of the release**:

```
git push -f origin master
```

## 🚀 Making a production-ready release

Create the release branch out from develop.

````
# On branch develop
git checkout develop
git checkout -b release-[YYYMMDD]-[HHMM]
````

Branch name example: `release-20140325-1804`

As git-flow dictates: this new branch may exist there for a while, until the
release may be rolled out definitely. During that time, bug fixes may be applied
in this branch (rather than on the `develop` branch). Adding large new features
here is strictly prohibited. They must be merged into develop, and therefore,
wait for the next big release.

Once everything is ready, merge the release branch into master.

````
git checkout master
git merge release-[YYYYMMDD]-[HHMM]
````

Tag the release version.

````
git tag -a [YYYYMMDD]-[HHMM]
````

Merge the release branch into develop.

````
git checkout develop
git merge release-[YYYYMMDD]-[HHMM]
````

Finally, push the changes to the remote repository.

````
git push origin master
git push origin develop
git push origin --tags
````
