---
title: Jenkins Pipeline Cheatsheet
description: Jenkins pipelines notes and findings.
date: 2020-01-20 22:11
category: Continuous Integration
tag:
  - continuous-integration
  - ci-cd
  - jenkins
layout: page
---

# Jenkins Pipeline Cheatsheet

- [Archiving Artifacts](#archiving-artifacts)
- [Variables](#variables)
    - [Global Variables](#global-variables)
    - [Environment Variables](#environment-variables)
    - [Storing shell STDOUT values](#storing-shell-stdout-values)
- [Triggers](#triggers)
    - [Conditional Triggers](#conditional-triggers)

## Archiving Artifacts

```groovy
archiveArtifacts artifacts: "**/${env.RESULTS_ARCHIVE}"
```

_**Note:** Always use double quotes with string interpolation._

## Variables

### Global Variables

```groovy
VAR="value"
pipeline {
    // ...
}
```

Global and mutable variables need to be outside of the pipeline.

### Environment Variables

```groovy
pipeline {
    environment {
        VAR="VALUE"
    }
    stage {
        steps {
            VAR = "NEW VALUE"  // won't work
            withEnv(['VAR="NEW NEW VALUE"']) {
                // VAR will only be "NEW NEW VALUE" in this block
                // Not helpful for setting a global to be used later
            }
        }
    }
}
```

Environment variables can only be modified temporarily with a `withEnv` block.

### Storing shell STDOUT values

```groovy
def var = sh([script: "echo 'Variable value'", returnStdout: true]).trim()
```

## Triggers

### Conditional Triggers

```groovy
pipeline {
    agent "your-agent"
    triggers {
        cron(env.BRANCH_NAME == "master" ? "0 23 * * *" : "")
    }
    stage {
        // ...
    }
}
```

Triggers a build on the `master` branch every night at 23:00. A build will only be triggered for the `master` branch, and no other branches in a multibranch pipeline.

_See also:_
* [Source](https://issues.jenkins-ci.org/browse/JENKINS-42643?focusedCommentId=293221#comment-293221)
* [crontab guru](https://crontab.guru/)
