# renovate_minimal_rep
reproduction of issue with renovate

I am running Renovate on GitLab (image renovate:35.115.2).

I have target repository and I want to test baseBranch and useBaseBranchConfig parameters to allow people to use config for Renovate from specific branches and remove need to update default repo branch every time they want to change Renovate config.

Target repository default branch (main) has renovate.json.
baseBranch renovate-updates-collection has also rnovate.json updated with some additional fields
Once renovate runs, job fails with `"msg": "Repository has unknown error"`

This repository contains minimal reproduction of issue.

Error is happening when Renovate job tries to run, with following settings(which reflect this repository setup):
- "useBaseBranchConfig": "merge"
- "enabledManagers": ["regex"], (set up to check file imagevector.yaml for dependencies)
- imagevector.yaml is present in baseBranch
- baseBranch renovate.json contains "packageRules": [] for regex manager
- default (main) branch renovate.json does not contain "packageRules": [] for regex manager

Failed Renovate job ID: ff0e4d1f-744f-4307-a452-4d98e94df9d3

Here is full message from failed job:

```
DEBUG: Renovate exiting
INFO: Renovate is exiting with a non-zero code due to the following logged errors
{
  "loggerErrors": [
    {
      "name": "renovate",
      "level": 50,
      "logContext": "1b2c75dec36142e0829fd261c10d63b4",
      "repository": "pbagona/renovate_minimal_rep",
      "err": {
        "message": "slugify: string argument expected",
        "stack": "Error: slugify: string argument expected\n    at replace (/opt/containerbase/tools/renovate/35.131.0/node_modules/slugify/slugify.js:20:13)\n    at generateBranchName (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/updates/branch-name.ts:56:31)\n    at applyUpdateConfig (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/updates/flatten.ts:60:21)\n    at flattenUpdates (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/updates/flatten.ts:127:28)\n    at processTicksAndRejections (node:internal/process/task_queues:95:5)\n    at branchifyUpgrades (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/updates/branchify.ts:22:19)\n    at lookup (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/process/extract-update.ts:195:36)\n    at extractDependencies (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/process/index.ts:142:31)\n    at Object.renovateRepository (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/repository/index.ts:61:9)\n    at attributes.repository (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/global/index.ts:184:11)\n    at start (/opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/workers/global/index.ts:169:7)\n    at /opt/containerbase/tools/renovate/35.131.0/node_modules/renovate/lib/renovate.ts:18:22"
      },
      "msg": "Repository has unknown error"
    }
  ]
}

```
