pipeline {
    agent none
    environment {
        AWS_REGION = 'us-east-1'
        DEV_DOMAIN = 'bctmm-qa.com'
        DEV_HOST = "${env.ghprbSourceBranch.tokenize('/').last().replaceAll('[_.]', '-')}"
    }
    stages {
        stage("Checkout") {
            agent any
            steps {
                dir('trading-screen') {
                    sh 'rm -rf *'
                    git branch: env.ghprbSourceBranch, credentialsId: env.ghprbCredentialsId, url: env.ghprbAuthorRepoGitUrl
                }
            }
        }

//        stage('Build') {
//            agent {
//                docker {
//                    image 'node:8.11.1-alpine'
//                }
//            }
//            steps {
//                dir('trading-screen/alphapoint') {
//                    sh 'sh ./build.sh'
//                }
//                dir('trading-screen/alphapoint/v2retailTemplate') {
//                    sh 'sh ./build.sh'
//                }
//            }
//            post {
//                failure {
//                    echo 'Failure with building project'
//                }
//            }
//        }

        stage('Deploy') {
            agent {
                docker {
                    image 'seamusv/aws_tools:latest'
                }
            }
            steps {
                sh 'static_website -d $DEV_DOMAIN -h $DEV_HOST -i trade.html'
                s3Upload(
                        bucket: "${env.DEV_HOST}.${DEV_DOMAIN}",
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