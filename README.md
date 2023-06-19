# renovate_minimal_rep
reproduction of issue with renovate

I am running Renovate on GitLab (image renovate:35.115.2).

I have target repository and I want to test baseBranch and useBaseBranchConfig parameters to allow people to use config for Renovate from specific branches and remove need to update default repo branch every time they want to change Renovat config.

In repo where I schedule Renovate, I have config like this

```
module.exports = {
    endpoint: 'https://gitlab.devops.******.de/api/v4/',
    platform: 'gitlab',
    extends: ['config:base'],
    baseBranches: ['renovate-updates-collection'],
    useBaseBranchConfig: 'merge',
    repositories: [
      {
        repository: 'peter.bagona/pbagonacv',
      }
    ],
    }
```

CI/CD job is defined like this:

```
image: "***/renovate:35.115.2"

variables:
  RENOVATE_BASE_DIR: $CI_PROJECT_NAME
  LOG_LEVEL: debug
  # github app token
  GITHUB_COM_TOKEN: "$GITHUB_TOKEN"

renovate:
  stage: deploy
  resource_group: production
#  only:
#    - schedules
  script:
    - renovate $RENOVATE_EXTRA_FLAGS
```

Target repository default branch (main) has renovate.json.
baseBranch renovate-updates-collection has also rnovate.json updated with some additional fields.
I have copied this setup into this repository, with configs in main and renovate-updates-collection and one Dockrfile for test. I have same setup on Gitlab and issue is happenning there.

Once renovate runs, job fails with `"msg": "Repository has unknown error"`

I tried to compare "merged" config that Renovate uses in failed job with config that I want to achieve (it is in baseBranch) and it is more or less same. This config has been tested in different repositories and is working fine when used without useBaseBranchConfig feature and it is in main branch.

Here is full message from failed job:

```
DEBUG: found labels in manifest (repository=peter.bagona/pbagonacv, baseBranch=renovate-updates-collection)
       "labels": {"io.buildah.version": "1.29.0"}
DEBUG: PackageFiles.add() - Package file saved for base branch (repository=peter.bagona/pbagonacv, baseBranch=renovate-updates-collection)
DEBUG: Package releases lookups complete (repository=peter.bagona/pbagonacv, baseBranch=renovate-updates-collection)
DEBUG: branchifyUpgrades (repository=peter.bagona/pbagonacv, baseBranch=renovate-updates-collection)
ERROR: Repository has unknown error (repository=peter.bagona/pbagonacv)
       "err": {
         "message": "slugify: string argument expected",
         "stack": "Error: slugify: string argument expected\n    at replace (/opt/containerbase/tools/renovate/35.115.2/node_modules/slugify/slugify.js:20:13)\n    at generateBranchName (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/updates/branch-name.ts:56:31)\n    at applyUpdateConfig (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/updates/flatten.ts:60:21)\n    at flattenUpdates (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/updates/flatten.ts:127:28)\n    at branchifyUpgrades (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/updates/branchify.ts:22:19)\n    at lookup (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/process/extract-update.ts:195:36)\n    at extractDependencies (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/process/index.ts:130:31)\n    at Object.renovateRepository (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/repository/index.ts:61:9)\n    at attributes.repository (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/global/index.ts:184:11)\n    at start (/opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/workers/global/index.ts:169:7)\n    at /opt/containerbase/tools/renovate/35.115.2/node_modules/renovate/lib/renovate.ts:18:22"
       }
DEBUG: Repository result: unknown-error, status: onboarded, enabled: true, onboarded: true (repository=peter.bagona/pbagonacv)
DEBUG: Repository timing splits (milliseconds) (repository=peter.bagona/pbagonacv)
       "splits": {"init": 3984, "extract": 950},
       "total": 6341
```
