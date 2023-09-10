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
        SONAR_URL = "http://34.202.231.69:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }

    // nexus 

    


      stage('Upload Jar to Nexus'){
    steps{
        script{
            def readPomVersion = readMavenPom file: 'pom.xml'

            nexusArtifactUploader artifacts: [
                [artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']
            ],
            credentialsId: 'nexus-auth', 
            groupId: 'com.example', 
            nexusUrl: 'http://54.83.103.209:8081/repository/demoapp-release/', // Corrected URL
            nexusVersion: 'nexus3', 
            protocol: 'http', 
            repository: 'demoapp-release', 
            version: "${readPomVersion.version}"
        }
    }
}


// 
    }
}
