pipeline {
    agent any
    // tools {
    //     maven 'mvn'
    // }
    stages{
        stage('GIT checkout') {
           steps{
                echo "====++++executing git checkout++++===="
                git 'https://github.com/njokuifeanyigerald/ansibleCalTech.git'
            }
        }
        stage('Build Package') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Docker build and Tag') {
            steps{
                sh 'docker build -t ${JOB_NAME}:v1.${BUILD_NUMBER} .'
                sh 'docker tag ${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:v1.${BUILD_NUMBER} '
                sh 'docker tag ${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:latest '
            }
        }
        stage('push container') {
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubcred')]) {
                  sh 'docker login -u bopgeek -p ${dockerhubcred}'
                  sh 'docker push bopgeek/${JOB_NAME}:v1.${BUILD_NUMBER}'
                  sh 'docker push bopgeek/${JOB_NAME}:latest'
                  sh 'docker rmi ${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:latest'
                }      
            }
        }
        stage('Docker Deploy') {
            steps{
                // ansiblePlaybook credentialsId: 'ansible-host', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory.txt', playbook: 'deploy.yml'
                ansiblePlaybook become: true, credentialsId: 'ansible-host', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory.txt', playbook: 'deploy.yml'
            }
        }          
    }
}
