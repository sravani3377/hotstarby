pipeline {
    agent any

    stages {
        stage('Pre-clean Workspace') {
            steps {
                sh '''
                    echo "🔧 Fixing permissions..."
                    sudo chown -R jenkins:jenkins /var/lib/jenkins/workspace/hotstar1 || true
                    sudo chmod -R 755 /var/lib/jenkins/workspace/hotstar1 || true
                    echo "🧹 Cleaning old target directory..."
                    rm -rf /var/lib/jenkins/workspace/hotstar1/target || true
                '''
            }
        }

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sravani3377/hotstarby.git'
                sh 'pwd'
                sh 'ls -l'
                sh 'ls -R'
            }
        }

        stage('Build WAR') {
            steps {
               sh 'mvn package -Dmaven.clean.failOnError=false'

            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker rmi -f hotstar:v1 || true
                    docker build -t hotstar:v1 -f /var/lib/jenkins/workspace/hotstar1/Dockerfile /var/lib/jenkins/workspace/hotstar1
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
