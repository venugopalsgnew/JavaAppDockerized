pipeline {
    agent any 
    tools {
        maven 'Maven'
    }
    stages {
        stage('Git Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/venugopalsgnew/Javaapplication_dockerise.git']])
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
       stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Image build') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Docker Container launch') {
            steps {
                sh 'docker run -d -p 8050:8080 --name mycontainer myapp'
            }
        }
        


    }
    
    
}
