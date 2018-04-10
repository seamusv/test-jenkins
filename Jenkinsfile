pipeline {
    agent none
    stages {
        stage("Checkout") {
            agent any
            steps {
                sh 'pwd'
                sh 'export'
                dir('trading-screen') {
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
                    image 'seamusv/public_s3'
                    args '-d bctmm-qa.com -h ${env.ghprbSourceBranch} -i trade.html'
                }
            }
            steps {
                s3Upload(
                        bucket: '${env.ghprbSourceBranch}.bctmm-qa.com',
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