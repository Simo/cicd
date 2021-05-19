pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: groovy
    image: jenkins/inbound-agent:4.7-1-jdk11
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
        stage('Build') {
            steps {
                container('groovy') {
                    sh './mvnw -B compile'
                }
            }
        }
        stage('Package') {
            steps {
                container('groovy') {
                    sh './mvnw package -DskipTests=true'
                }
            }
        }
        stage ('Creating build tag') {
            steps {
                createTag nexusInstanceId: 'nexus3', tagAttributesJson: '{"createdBy" : "SimoneBierti"}', tagName: 'build-125'
            }
            
        }
        stage ('Publishing') {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusPublisher(nexusInstanceId: 'nexus3',
                               nexusRepositoryId: 'maven-releases',
                               packages: [[$class: 'MavenPackage',
                                           mavenAssetList: [[classifier: '', extension: '', filePath: artifactPath]],
                                           mavenCoordinate: [artifactId: pom.artifactId, groupId: pom.groupId, packaging: pom.packaging, version: pom.version]]],
                               tagName: 'build-125')
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                
                }
                
            }
        }
    }
    
}
