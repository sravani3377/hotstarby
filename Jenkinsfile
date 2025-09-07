pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sravani3377/hotstarby.git'
              
            }
        }

        stage('Build WAR') {
            steps {
               sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    
                    docker rmi -f hotstar:v1 || true
                    docker build -t hotstar:v1 .
                '''
            }
        }

   stage('Push to Docker Hub') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker tag hotstar:v1 $DOCKER_USER/hotstar:latest
                docker push $DOCKER_USER/hotstar:latest
                docker logout
            '''
        }
    }
}


        stage('Deploy Container') {
            steps {
                sh '''
              
                    docker rm -f con8 || true
                    docker run -d --name con8 -p 9943:8080 hotstar:v1
                '''
            }
        }
    }
}
