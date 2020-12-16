# Life Cycle - Pipeline: Develop, Build, Test, Deploy, Release

Content:

- [Introduction](../application-lifecycle.md)
- [Evolution and adaptation](al-evolution-and-adaptation.md)
- [CI-CD-CD](al-cicdcd.md)
- [Releasability](al-releasability.md)
- **Pipeline: Develop, Build, Test, Deploy, Release**
- [Changelog](al-changelog.md)

TBD

```groovy
#!groovy

@Library('github.com/red-panda-ci/jenkins-pipeline-library@v2.6.2') _

// Initialize global config
cfg = jplConfig('general-concepts', 'doc', '', [slack: '#integrations', email:'redpandaci+general-concepts@gmail.com'])

pipeline {
    agent none

    stages {
        stage ('Initialize') {
            agent { label 'master' }
            steps  {
                jplStart(cfg)
            }
        }
        stage ('Release confirm') {
            when { expression { cfg.BRANCH_NAME.startsWith('release/v') || cfg.BRANCH_NAME.startsWith('hotfix/v') } }
            steps {
                jplPromoteBuild(cfg)
            }
        }
        stage ('Release finish') {
            agent { label 'master' }
            when { expression { cfg.BRANCH_NAME.startsWith('release/v') || cfg.BRANCH_NAME.startsWith('hotfix/v') } }
            steps {
                jplCloseRelease(cfg)
            }
        }
    }

    post {
        always {
            jplPostBuild(cfg)
        }
    }

    options {
        timestamps()
        ansiColor('xterm')
        buildDiscarder(logRotator(artifactNumToKeepStr: '20',artifactDaysToKeepStr: '30'))
        disableConcurrentBuilds()
        skipDefaultCheckout()
        timeout(time: 1, unit: 'DAYS')
    }
}
```

![Project pipeline](img/general-concepts-pipeline.png?raw=true "Project pipeline")

Other pipeline examples:

![Pipeline example](img/realworld-pipeline-flow.png?raw=true "Pipeline example")

![Pipeline example with Jenkins](img/jenkins-pipeline-example.png?raw=true "Pipeline example with Jenkins")

## References

- [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/)
- [Git Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow)

---

Next: [Changelog](al-changelog.md) - Go to [Main page](../toc.md)
