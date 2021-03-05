/*
 CI simple pipeline
 */


 // Clone from Git
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