+++
title = "Rolling back"
chapter = false
weight = 42
+++

In most cases a production deployment will succeed right away and all application users will be able to use the new features right away.

However, there will be cases when a production deployment will have blocker issues and problems. In this case you should be able to roll back to the previous version until 
you have some time to solve the issue (possibly in a staging environment and not production)

## Choosing a version to rollback to 

Since we are using GitOps, rolling back to a previous version will happen with Git operations.

The first step is to decide which Git version we will rollback. In most cases it is the last one, but this is not a strict requirement.

You can find the last commit hash in multiple ways. The simplest is to use the normal Git tools (such as the Github UI).

![Git](/images/basic_cd/git-history.png)

In most cases however you might want to look at the Codefresh GitOps dashboard as it also shows which features were deployed to a particular Git commit. This way you can confirm that the git hash that you will revert to has a specific feature (or doesn't if that feature is the one that caused the issue in the first place).

![GitOps history](/images/basic_cd/gitops-history.png)

If you visit the timeline of your application in the Codefresh GitOps dashboard
you will see the complete history of deployments along with Git hashes at the bottom of the screen.

## Rolling back to the previous version

Once you have the desired Git hash you can use your favorite Git tool to revert (or reset) to that commit.

For example if you want to revert the last commit identified by e4087e7 you can simply execute 

```
git revert --no-edit e4087e7
git push
```

You can also leave out the `--no-edit` option so you have the ability to put a Git message that hopefully explains why this release was discarded.

See the [git revert documentation](https://git-scm.com/docs/git-revert) for more details. 

## Rolling back to any version

It is also possible to go back to any previous release (and not just discard the last one) using the git reset command

```
git reset --hard 3515c7a
git push
```

See the [git reset documentation](https://git-scm.com/docs/git-reset) for more details. 



