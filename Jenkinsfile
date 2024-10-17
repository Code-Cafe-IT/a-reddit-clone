pipeline {
    agent any
    tool{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-app"
        RELEASE = "1.0.0"
        DOCKER_USER = "minhduccloud"
        DOCKER_PASS = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	// JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
    }
    stages{
        stage("clean workspace"){
            steps{
                cleanWs()
            }
        }
        stage("Checkout from git"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Code-Cafe-IT/a-reddit-clone.git'
            }
        }
        stage("Sonarqube Analysis"){
            steps{
                withSonarQubeEnv(credentialsId: 'SonaQube-Token') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Reddit-Clone-CI \
                    -Dsonar.projectKey=Reddit-Clone-CI'''
                }
            }
        }
        stage("Quality Gate"){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonaQube-Token'
                }
            }
        }
        stage("Install Dêpndencies"){
            steps{
                sh "npm install"
            }
        }
        stage("Trivy fs scan"){
            steps{
                sh "trivy fs . > trivyfs.txt"
            }
        }
    }
}