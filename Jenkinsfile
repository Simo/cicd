pipeline {
  agent {
    kubernetes {
      yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: groovy
    image: jenkins/inbound-agent:4.7-1-jdk11
    command: [\'cat\']
    tty: true
    imagePullPolicy: Always
'''
    }

  }
  stages {
    stage('Source checkout') {
      steps {
        container(name: 'groovy') {
          checkout scm
        }

      }
    }

    stage('Build') {
      parallel {
        stage('Build') {
          steps {
            container(name: 'groovy') {
              sh './mvnw -B compile'
            }

          }
        }

        stage('indica la versione') {
          steps {
            input 'Indica il # di versione'
          }
        }

      }
    }

    stage('Package') {
      steps {
        container(name: 'groovy') {
          sh './mvnw package -DskipTests=true'
        }

      }
    }

    stage('Publishing') {
      steps {
        script {
          pom = readMavenPom file: "pom.xml";
          filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
          echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
          artifactPath = filesByGlob[0].path;
          artifactExists = fileExists artifactPath;
          if(artifactExists) {
            echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
            nexusArtifactUploader(
              nexusVersion: NEXUS_VERSION,
              protocol: NEXUS_PROTOCOL,
              nexusUrl: NEXUS_URL,
              groupId: pom.groupId,
              version: pom.version,
              repository: NEXUS_REPOSITORY,
              credentialsId: NEXUS_CREDENTIAL_ID,
              artifacts: [
                [artifactId: pom.artifactId,
                classifier: '',
                file: artifactPath,
                type: pom.packaging],
                [artifactId: pom.artifactId,
                classifier: '',
                file: "pom.xml",
                type: "pom"]
              ]
            );
          } else {
            error "*** File: ${artifactPath}, could not be found";
          }
        }

      }
    }

  }
  environment {
    NEXUS_VERSION = 'nexus3'
    NEXUS_PROTOCOL = 'http'
    NEXUS_URL = '192.168.1.76:8081'
    NEXUS_REPOSITORY = 'maven-nexus-repo'
    NEXUS_CREDENTIAL_ID = 'nexus'
  }
  options {
    skipDefaultCheckout(true)
  }
}