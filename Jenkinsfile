/*
 CI simple pipeline
 */


 // Clone from Gits
 // Build and Unit Test (Maven/Junit)
 // Static Code Analysis (SonarQube)
 // Integration Test (Maven/JUniy) 

 // Publish Artifact to Repository Manager (Nexus) 

pipeline{
    agent any
    tools{
        maven  'my-maven-3'
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "host.docker.internal:8081"
        NEXUS_REPOSITORY = "http://localhost:8081/repository/mylocalrepo-snapshots"
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
    }


    stages{
        stage("Clone from Gi"){
            steps{
                git branch:'master', url:'https://github.com/abdallawi/08-jenkins-cicd-pipeline-maven-01-continious-integration.git'
            }
            
        }

        stage("Build and Unit Test (Maven/Junit)"){
                steps{
                    sh 'mvn clean package'
                    junit '**/target/surefire-reports/TEST-*.xml' // archiver les rapport de test
                }
                
        }

        stage("Static Code Analysis (SonarQube)"){
                steps{
                    withSonarQubeEnv('my_sonarqube_in_docker') {                         
             	      
             	          sh "mvn clean verify -DskipTests=true  sonar:sonar -Dsonar.host.url=http://host.docker.internal:9000   -Dsonar.projectName=08-jenkins-cicd-pipeline-maven-01-continious-integration -Dsonar.projectKey=07-static-code-analysis-sonarqube -Dsonar.projectVersion=$BUILD_NUMBER";
                         
             	        }  
                }
                
        }
        
        stage("Checking the Quality Gate") {
                  steps {
                       echo "====++++  Checking the returned SonarQube Quality Gate ++++===="
                      timeout(time: 1, unit: 'HOURS') {
                          // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                          // true = set pipeline to UNSTABLE, false = don't
                          waitForQualityGate abortPipeline: true
                      }
        }

        stage("Integration Test (Maven/JUnit)"){
            steps{
                echo "====++++  Integration Test (Maven/JUnit) ++++===="
                sh "mvn clean verify -Dsurefire.skip=true"    // Do not repeat the unit tests, they have been already done
                junit "**/target/failsafe-reports/*.xml" // Archive the test reports
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
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}