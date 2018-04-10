pipeline {
    agent any
    stages {
        stage("Checkout code") {
            steps {
                git branch: env.ghprbActualCommit, credentialsId: env.ghprbCredentialsId, url: env.ghprbAuthorRepoGitUrl
            }
        }
        stage("Build") {
            steps {
                sh 'pwd'
                sh 'export'
            }
        }
    }
}