@Library('jenkins-shared-library') _

pipeline {
    agent any

    triggers {
        githubPush()
        pollSCM('H/5 * * * *')
    }

    environment {
        IMAGE_NAME    = "zhehaochen/product-service"
        GIT_SHORT     = "${env.GIT_COMMIT?.take(7) ?: 'unknown'}"
        IMAGE_TAG     = "build.${env.BUILD_NUMBER}-git.${GIT_SHORT}"
        ENV_TAG       = "${env.BRANCH_NAME == 'main' ? 'prod-latest' : env.BRANCH_NAME == 'develop' ? 'dev-latest' : 'staging-latest'}"
    }

    stages {

        stage('Build') {
            steps {
                script { buildStage() }
            }
        }

        stage('Test') {
            steps {
                script { testStage() }
            }
        }

        stage('Security Scan') {
            steps {
                script { securityScanStage() }
            }
        }

        stage('Container Build') {
            when {
                anyOf {
                    branch 'develop'
                    branch pattern: 'release/.*', comparator: 'REGEXP'
                    branch 'main'
                }
            }
            steps {
                script { containerBuildStage(env.IMAGE_NAME, env.IMAGE_TAG, env.ENV_TAG) }
            }
        }

        stage('Container Push') {
            when {
                anyOf {
                    branch 'develop'
                    branch pattern: 'release/.*', comparator: 'REGEXP'
                    branch 'main'
                }
            }
            steps {
                script { containerPushStage(env.IMAGE_NAME, env.IMAGE_TAG, env.ENV_TAG) }
            }
        }

        stage('Deploy to Dev') {
            when { branch 'develop' }
            steps {
                script { deployStage('dev', env.IMAGE_NAME, env.IMAGE_TAG) }
            }
        }

        stage('Deploy to Staging') {
            when { branch pattern: 'release/.*', comparator: 'REGEXP' }
            steps {
                script { deployStage('staging', env.IMAGE_NAME, env.IMAGE_TAG) }
            }
        }

        stage('Approval for Production') {
            when { branch 'main' }
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    input message: 'Deploy to Production?', ok: 'Approve'
                }
            }
        }

        stage('Deploy to Production') {
            when { branch 'main' }
            steps {
                script { deployStage('prod', env.IMAGE_NAME, env.IMAGE_TAG) }
            }
        }
    }

    post {
        success { echo "Pipeline succeeded: ${env.BRANCH_NAME} -> ${env.IMAGE_TAG}" }
        failure  { echo "Pipeline failed on branch: ${env.BRANCH_NAME}" }
    }
}
