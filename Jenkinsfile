pipeline {
    agent any 
    
    tools {
        jdk 'jdk17'  // Changed from 'jdk11' to 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME = tool name: 'sonar-scanner'
    }
    
    stages {
        
        stage("Git Checkout") {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/sayan754/Petclinic.git'
            }
        }
        
        stage("Compile") {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage("Test Cases") {
            steps {
                sh "mvn test"
            }
        }

        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
                }
            }
        }
        stage("Build") {
            steps {
                sh "mvn clean install"
            }
        }
        stage("Docker Build and Push") {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'd56fa21a-05af-4e97-83ab-712b48193b20', toolName: 'docker') {
                        
                        sh "docker build -t image1 ."
                        sh "docker tag image1 sayan510/pet-clinic123:latest "
                        sh "docker push sayan510/pet-clinic123:latest "
                    }
                }
            }
        }
        stage("TRIVY") {
            steps {
                sh " trivy image sayan510/pet-clinic123:latest"
            }
        }
        
    }
}
