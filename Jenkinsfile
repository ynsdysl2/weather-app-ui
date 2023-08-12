pipeline {
    agent any

    environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short=7 HEAD").trim()
    }

    stages {
        stage('Build') {
            steps {
                echo 'Starting to build weather-ui docker image..'
                dir ("weather-ui"){
                    sh 'docker build -t localhost:8082/repository/docker/weather-ui:${tag} .'
                    sh 'docker login -u admin -p admin http://localhost:8082'
                    sh 'docker image push localhost:8082/repository/docker/weather-ui:${tag}'
                    sh 'docker tag localhost:8082/repository/docker/weather-ui:${tag} localhost:8082/repository/docker/weather-ui:latest'
                    sh 'docker image push localhost:8082/repository/docker/weather-ui:latest'
                    sh 'docker system prune -af'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Starting to deploy weather-ui...'
                dir ("k8s"){
                    sh 'sudo kubectl apply -f deployment.yaml'
                    sh 'sudo kubectl apply -f service.yaml'
                    sh 'sudo kubectl apply -f ingress.yaml'
                }
            }
        }
    }
}