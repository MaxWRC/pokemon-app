pipeline {
    agent any

    environment {
        IMAGE_NAME = "yaunpetesurleyz/pokedex"
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker build -t test-pokedex -f test.Dockerfile .'
                sh 'docker run --rm test-pokedex'
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker pull $IMAGE_NAME
                    docker stop pokedex || true
                    docker rm pokedex || true
                    docker run -d -p 5000:5000 --name pokedex $IMAGE_NAME
                '''
            }
        }
    }
}

