pipeline {
    agent any
    stages {
        stage('Clone Repos') {
            steps {
                checkout([
          $class: 'GitSCM',
          branches: [[name: '*/master']],
          userRemoteConfigs: [[url: 'https://github.com/pratyushsingha/Threads.git']],
          extensions: [
            [$class: 'SubmoduleOption', recursiveSubmodules: true]
          ]
        ])
            }
        }
        stage('Docker Compose Build && Push') {
            steps {
                echo 'Building the Docker Image...'
                withCredentials([usernamePassword(
                credentialsId:'dockerHubCred',
                passwordVariable:'dockerHubPass',
                usernameVariable:'dockerHubUser')]) {
                    sh 'docker login -u $dockerHubUser -p $dockerHubPass'
                    sh 'docker compose build'
                    sh 'docker tag threads:latest $dockerHubUser/threads:latest'
                    echo 'Pushing the Docker Image to Docker Hub...'
                    sh 'docker push $dockerHubUser/threads:latest'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy on ec2...'
                dir('/home/ec2-user/Threads') {
                    sh 'docker compose down --remove-orphans'
                    sh 'docker compose pull'
                    sh 'docker compose up -d --remove-orphans'
                }
            }
        }
    }
}
