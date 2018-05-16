# LinearGitFlow

## I. Problematics

- Simplify the workflow to make it more adaptable to the team needs.
- Have a readable changelog.
- Have more control in feature-team context.

## II. Technical overview

- `master` branch = Main branch : only contains features technically validated.
- A **branch** per environment and **per feature** (like in *Gitflow*).
- A **tag per version**.
- A **technical validation** by a *Merge Request* (MR) on Gitlab using **labels**.
- A **functional validation** by the Product Owner.
- **Rebase to integrate** a feature on master.
- Gitlab-CI integration for `master`, `staging` and `prod` branch/tags.

### III. How to get latest devs
To fully synchronize your local repository with distant repository, use the following command:

```bash
git fetch -p -a --tags
```

### IV. How to develop a feature

- Create a feature branch named `<feature-name>`:
    - `git checkout master`
    - `git pull origin master`
    - `git checkout -b <feature-name>`   
- Code.
- Rebase your branch very often to get your feature branches up-to-date. You can use:
    - `git checkout <feature-name>`
    - `git rebase master`
    - `git push -f origin <feature-name>`

> ![danger](.README/danger.png) Do not rebase a branch when several devs are working on it. Shared branch like master must be protected against push
forces in Gitlab.
>
> ![warning](.README/warning.png) In general, be prudent with rebases... A great power comes with a great responsibilities.


### V. How to do a Merge Request

#### 1.Create your MR

- ![gitlab](.README/gitlab.png) When you think that the development of your feature is ok, create a MR on Gitlab and select master as target branch.


#### 2.Prerequisites

- ![gitlab](.README/gitlab.png) Create a Gitlab Label for each team member. This label will represent their approbation to validate the MR. For exemple, if the dev's
name is Regis, the label could be "Regis-approved".

#### 3.Validation
- Do your code review.
- If all is OK apply your validation label, else, comment the code.

> ![danger](.README/danger.png) Do not click on the `Accept merge request` button !!! If the feature branch is not up-to-date, this will create a merge, which is
not desired since we are using rebases. 
>
> ![info](.README/info.png) This will be certainly solved when the repo will be moved on Github (I think the
non-free version has the rebase option as Gitlab non-free version has it).


### VI. How to put a dev in validation (Rec)

**master**, **staging** and **prod** branches/tags are used as references to point on the commit on which the environment is deployed by Gitlab-CI.

#### 1.Use case 1: Nothing is already tested in staging

If you want to put a spefic branch or the master branch in validation, force move the `staging` branch on it by deleting and recreating it.

Example :

- `git branch -d staging`
- `git checkout <feature-name>`
- `git branch staging`
- `git checkout staging`
- `git push -f origin staging`

#### 2.Use case 2: Several features must be tested in the same time

To have multiple different features on the `staging` you need to use **cheery-picks** :

- `git checkout staging`
- `git cherry-pick <commit-1> <commit-2> [...]`
- `git push -f`

> ![info](.README/info.png) `<commit-1>`, `<commit-2>`, [...] are the ids of the feature branch commits that were not already copied in `staging`.
>
> You can also use `git rebase -i` to reorder and group commits that come from the same feature to be more coherent and
delete the commits of feature that are already validated and integrated on master.

### VII. How to integrate a feature in master

Master branch contains one commit per feature. All those features must be only integrate in `master` when it is both validated technically and
functionally during the Merge Request and the validation phase.


#### 1.Manually integrate your feature branch

> ![info](.README/info.png) We name `<base-commit-id>` is the UUID of the commit where your feature branch forks from `master`.

Use the following commands:
- `git checkout master`
- `git pull origin master`
- `git checkout <feature-name>`
- `git rebase -i <base-commit-id>` _// Optional_
- `git rebase master`
- `git push -f origin <feature-name>`
- `git checkout master`
- `git merge --ff-only <feature-name>`
- `git push origin master`

> ![warning](.README/warning.png) Don't forget to clean your feature after (delete branch locally and on repo)
>
> - `git branch -d <feature-name>`
> - `git push origin :<feature-name>`

#### 2.How to move to production

![info](.README/info.png) Actually, the `prod` is a **tag** (as it is only used as ref, it can be used in the same way as `staging` branch...)

To put the master in production:

- `git checkout master`
- `git pull origin master`
- `git tag -d prod`
- `git push -d origin prod`
- `git tag prod`
- `git push origin tag`
- `git tag <version-number>` 
- `git push origin <version-number>`

The `<version-number>` follows this format : `<major-version>.<minor-version>.<bug-fix>`
