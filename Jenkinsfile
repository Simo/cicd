pipeline {
    agent { kubernetes }
    stages {
        stage('build') {
            steps {
                sh 'mvn --version'
            }
        }
    }
}
