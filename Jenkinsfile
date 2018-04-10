pipeline {
    agent none
    stages {
        stage("Checkout") {
            agent any
            steps {
                sh 'pwd'
                sh 'export'
                dir('trading-screen') {
                    sh 'rm -rf *'
                    git branch: env.ghprbSourceBranch, credentialsId: env.ghprbCredentialsId, url: env.ghprbAuthorRepoGitUrl
                }
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:8.11.1-alpine'
                }
            }
            steps {
                dir('trading-screen/alphapoint') {
                    sh 'sh ./build.sh'
                }
                dir('trading-screen/alphapoint/v2retailTemplate') {
                    sh 'sh ./build.sh'
                }
            }
            post {
                failure {
                    echo 'Failure with building project'
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'seamusv/public_s3:latest'
                }
            }
            environment {
                GW_BUCKET_NAME="${env.ghprbSourceBranch.tokenize('/').last().replaceAll('[_.]', '-')}"
            }
            steps {
                sh 'AWS_REGION=us-east-1 /usr/local/bin/public_s3 -d bctmm-qa.com -h $GW_BUCKET_NAME -i trade.html'
                s3Upload(
                        bucket: "${env.GW_BUCKET_NAME}.bctmm-qa.com",
                        workingDir: 'trading-screen/alphapoint/v2retailTemplate/build',
                        includePathPattern: '**'
                )
            }
            post {
                failure {
                    echo 'Failure with upload to S3'
                }
            }
        }

    }
}