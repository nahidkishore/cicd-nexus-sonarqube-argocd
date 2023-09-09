pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                // Check out the source code from a Git repository
                git branch: 'main', url: 'https://github.com/nahidkishore/cicd-nexus-sonarqube-argocd.git'
            }
        }

        stage('Unit Test') {
            steps {
                // Run unit tests using Maven
                sh 'mvn test'
            }
        }

        stage('Integrated Testing') {
            steps {
                // Run integrated tests using Maven, skipping unit tests
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Maven Build'){

            steps{
                sh 'mvn clean install'
            }
        }

        


        stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://54.145.35.111:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }

    // nexus 

    stage('Upload jar File To nexus'){

         steps{
            script{


                nexusArtifactUploader artifacts:
                 [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']],
                  credentialsId: 'nexus-auth', 
                  groupId: 'com.example', 
                  nexusUrl: '100.25.166.83:8081', 
                  nexusVersion: 'nexus3', 
                  protocol: 'http', 
                  repository: 'demoapp-release', 
                  version: "${readPomVersion.version}"
            }

         }

       }
 
    }
}
