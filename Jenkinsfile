pipeline {
    agent any

    stages {
        stage("Code") {
            steps {
                echo "Cloning the code"
                git branch: 'main', url: 'https://github.com/LondheShubham153/django-notes-app.git'
            }
        }

        stage("Build") {
            steps {
                echo "Building Docker image"
                sh 'docker build -t my-note-app .'
            }
        }

        stage("Push to Docker Hub") {
            steps {
                echo "Pushing image to Docker Hub"
                withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker tag my-note-app:latest shubham60266/my-note-app:latest
                        docker push shubham60266/my-note-app:latest
                    '''
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the container"
                sh '''
                    docker stop $(docker ps -q --filter "publish=8000") || true
                    docker rm $(docker ps -aq --filter "publish=8000") || true
                    docker run -d --name notes-app -p 8000:8000 shubham60266/my-note-app:latest
                '''
            }
        }
    }
}

