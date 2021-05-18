pipeline {
    agent {
        docker { 
            image 'jenkins/agent:latest-jdk11'
        }
    }
    stages {
        stage('Source checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh './mvnw -B compile'
            }
        }
    }
}
