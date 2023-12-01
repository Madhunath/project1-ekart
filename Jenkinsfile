pipeline {
    agent any
    tools{
        jdk  'jdk11'
        maven  'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'git@github.com:Madhunath/project1-ekart.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://54.86.43.112:9000/ Dsonar.login=squ_6de38df3b5813ce3a38ddf8d3990a3ad5dae74f8 -Dsonar.projectName=Shopping-Cart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Shopping-Cart '''
            }
        }
        
                
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '2fe19d8a-3d12-4b82-ba20-9d22e6bf1672', toolName: 'docker') {
                        
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart madhunath/shopping-cart:latest"
                        sh "docker push madhunath/shopping-cart:latest"
                    }
                }
            }
        }
    
        stage('Docker Deploy to Container') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'e8442a03-f89f-46d7-853e-ca9d84d501d0', toolName: 'bigproject') {
                    sh "docker run -d --name shopping-cart -p 8070:8070 madhunath/shopping-cart:latest" }
                }                
            }
        }
    }
}
