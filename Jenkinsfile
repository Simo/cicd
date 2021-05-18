pipeline {
    agent {
        docker { 
            image 'maven:3.8.1-adoptopenjdk-11'
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
