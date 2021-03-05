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