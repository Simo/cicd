pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: groovy
    image: jenkinsci/jnlp-slave
    command: ['cat']
    tty: true
    imagePullPolicy: Always
"""
        }
    }
    options {
        skipDefaultCheckout true
    }
    stages {
        stage('Source checkout') {
            steps {
                container('groovy') {
                    checkout scm
                }
            }
        }
        stage('Run library tests') {
            steps {
                container('groovy') {
                    sh './gradlew test'
                }
            }
        }
    }
}
