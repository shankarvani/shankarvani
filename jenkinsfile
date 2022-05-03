pipeline {
    agent {
        label "master"
    }
    tools {
        // Declarative Pipeline (2.6+)
        // Pipeline Utility Steps Plugin
        // Nexus Artifact Uploader Plugin
        // Note: this should match with the tool name configured in your jenkins instance (JENKINS_URL/configureTools/)
        maven "Maven"
    }
    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your nexus is running
        NEXUS_URL = "localhost:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "DevServer"
        // Jenkins credential ID to Authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "nexus_credentials"
    }
    stages {
        stage('Init') {
            steps {
               echo 'This is an Initialization phase.'
            }
        }
        stage('Build') {
            steps {
                echo 'Build Phase....'
                sh '/opt/maven/bin/mvn clean install'	
            }
        }
        stage('Unit Test') {
            steps {
               echo 'Skipping Unit Tests for now....'
            }            
        }
        stage('Integration Test') {
            steps {
               echo 'Skipping Integration Tests for now....'
            } 
        }               
        stage('Publish to Nexus Repository') {
            steps {
                script {
                    echo 'Publish to Nexus Repository....'
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under Target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the file found
                    artifactPath = filesByGlob[0].path;
                    // Verify artifact name exist or not
                    artifactExists = filesExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version.${BUILD_NUMBER},
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID
                            artifacts: [
                                // Artifacts generated such as .jar, .war file
                                [artifactId: pom.artifactId, classifier: '', file: artifactPath, type: pom.packaging],
                                // Lets update the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId, classifier: '', file: "pom.xml", type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }               
            }
        }
        stage('Development Phase Deploy') {
            steps {
                echo 'Deployment Phase....'
            }
        } 
        stage('Staging Deploy') {
            steps {
                script {
                    // Before Staging Deploy stage need to do some Approval Configuration
                    echo 'Staging Phase....
                }
            }
        }
        stage('Production Deploy') {
            steps {
                // Before Production Deploy stage need to do some Approval Configuration
                echo 'Production Phase....'
            }
        }
        stage('Archive Artifacts') {
            steps {
                echo 'Archive Artifacts Phase....'
                //archive 'target/*.war'
            }
        }
    }    
}
