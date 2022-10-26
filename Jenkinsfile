pipeline {
    agent any
    tools {
        maven "MAVEN"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "52.23.155.248:8081"
        NEXUS_REPOSITORY = "maven-test"
        NEXUS_CREDENTIAL_ID = "Nexus_Cred"
    }
    
    stages {
        stage("Clone code from GitHub") {
            steps {
                script {
                    git branch: 'main', credentialsId: '432d3691-f5df-4767-9c42-863b2471af3c', url: 'https://github.com/mizraimn/maven-test';
                }
            }
        }
        stage("Maven Build") {
            steps {
                script {
                    sh "mvn package -DskipTests=true"
                }
            }
        }
        stage("Publish to Nexus Repository Manager") {
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
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: '52.23.155.248:8081',
                            groupId: 'pom.com.mycompany.app',
                            version: 'pom.1.0-SNAPSHOT',
                            repository: 'maven-central-repository',
                            credentialsId: 'Nexus_Cred',
                            artifacts: [
                                [artifactId: 'pom.my-app',
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: 'pom.my-app',
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
}

