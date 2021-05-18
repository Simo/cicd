pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: groovy
    image: registry.redhat.io/openshift3/jenkins-slave-base-rhel7:v3.11
    command: ['cat']
    tty: true
    imagePullPolicy: Always
    env:
    - name: JAVA_HOME
      value: /usr/lib/jvm/jre-1.8.0-openjdk
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
    post {
        always {
            junit 'build/test-results/**/*.xml'
        }
    }
}
