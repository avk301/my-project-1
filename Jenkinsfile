pipeline {
    agent any

    stages {
        stage('git pull & compile') {
            steps {
                git "https://github.com/Debasish960/my-project-1.git"
                sh "/opt/maven/apache-maven-3.8.1/bin/mvn clean compile"
            }
        }
        stage('package $ deploy artifact to NEXUS') {
            steps {
                sh "/opt/maven/apache-maven-3.8.1/bin/mvn package"
                sh "/opt/maven/apache-maven-3.8.1/bin/mvn deploy"
            }
        }
        stage('build docker image') {
            steps {
                sh "docker build -t debasish7/my-project-img ."
            }
        }
        stage('docker login & push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'DockerHub', variable: 'Docker')]) {
                sh "docker login -u debasish7 -p ${Docker}"
                }
                sh "docker push debasish7/my-project-img"
            }
        }
        stage('run a container over ssh') {
            steps {
                sshagent(['Docker_Server_SSH']) {
                    sh 'ssh -o StrictHostKeyChecking=no ec2-user@172.31.17.123 docker rm -f project-container || true'
                    sh 'ssh -o StrictHostKeyChecking=no ec2-user@172.31.17.123 docker run -d --name project-container -p 8000:8000 debasish7/my-project-img /bin/bash'
                }
            }
        }
    }
}