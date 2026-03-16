pipeline {
    agent any

    environment {
        IMAGE_NAME = "javaapp"
        IMAGE_REPO = "ajithkumar0104/week12cicd"
        CONTAINER_NAME = "javaapp-container"
        DOCKER_CREDS = "dockerhub_token"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ajithbhuvi232-cell/week12_cicd.git'
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

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDS, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker tag $IMAGE_NAME $IMAGE_REPO:latest'
                    sh 'docker push $IMAGE_REPO:latest'
                }
            }
        }

        stage('Pull Image') {
            steps {
                sh 'docker pull $IMAGE_REPO:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker stop $CONTAINER_NAME || true'
                sh 'docker rm $CONTAINER_NAME || true'
                sh 'docker run -d -p 3000:8080 --name $CONTAINER_NAME $IMAGE_REPO:latest'
            }
        }
    }
}