pipeline {
    agent any
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
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