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

    


      stage('Upload Jar to Nexus') {
        environment {
            NEXUS_URL = 'http://54.83.103.209:8081' // Replace with your Nexus server URL
        }
        steps {
            withCredentials([usernamePassword(credentialsId: 'nexus-auth', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    def version = pom.version

                    def nexusArtifact = [
                        artifactId: 'springboot',
                        classifier: '',
                        file: 'target/Uber.jar', // Replace with the actual path to your JAR file
                        type: 'jar'
                    ]

                    def nexusUpload = nexusArtifactUploader(
                        artifacts: [nexusArtifact],
                        credentialsId: 'nexus-auth', // Ensure this matches your Jenkins credentials ID
                        groupId: 'com.example',
                        nexusUrl: "${NEXUS_URL}",
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        repository: 'demoapp-release',
                        version: version
                    )

                    nexusUpload.setNexusAuth(NEXUS_USERNAME, NEXUS_PASSWORD)
                    nexusUpload.perform()
                }
            }
        }
    }

// 
    }
}
