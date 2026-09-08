pipeline {
    agent any

    stages {

        stage('Environment') {
            steps {
                sh 'whoami'
                sh 'id'
                sh 'git --version'
                sh 'docker --version'
                sh 'kubectl version --client'
            }
        }

        stage('Git-Pull') {
            steps {
                echo 'Pulling From Git...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Spring Boot Project...'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                sh './mvnw test'
            }
        }

        stage('Docker-Build') {
            steps {
                echo 'Building Docker Image...'

                sh '''
                    docker build \
                        -t razdeepak/jenkins-practice-01:${BUILD_NUMBER} \
                        .
                '''
            }
        }

        stage('Docker-Push') {
            steps {
                echo 'Pushing Docker Image...'

                sh '''
                    docker push \
                        razdeepak/jenkins-practice-01:${BUILD_NUMBER}
                '''
            }
        }

        stage('Kubernetes-Deploy') {
            steps {
                echo 'Deploying to Kubernetes...'

                sh '''
                    kubectl apply -f kubernetes/deployment.yaml
                    kubectl apply -f kubernetes/service.yaml

                    kubectl set image deployment/jenkins-practice \
                        jenkins-practice=razdeepak/jenkins-practice-01:${BUILD_NUMBER}

                    kubectl rollout status deployment/jenkins-practice
                '''
            }
        }

        stage('Kubernetes-Status') {
            steps {
                sh '''
                    kubectl get nodes
                    kubectl get pods -o wide
                    kubectl get deployments
                    kubectl get services
                '''
            }
        }
    }

    post {
        success {
            echo 'Build And Deployment Successful.'
        }

        failure {
            echo 'Build Or Deployment Failed.'
        }
    }
}