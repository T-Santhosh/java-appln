pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "your-docker-repo/helloworld"
    }
    stages {
        stage ('git checkout'){
            steps{
                script{
                    git branch: 'node-dev', url: 'https://github.com/T-Santhosh/multi-branch.git'
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t santhoshjava/multi:v2 .'
                    sh 'docker images'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                        sh "docker login -u santhosh2969 -p ${dockerPassword}"
                        sh 'docker push santhoshjava/multi:v2'
                    
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeCredentials(kubectlCredentials: [[ credentialsId: 'kubernetes', namespace: 'ms' ]]) {
                        sh 'kubectl apply -f kube.yaml'
                        sh 'kubectl get pods -o wide'
                    
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'kubectl get all'
            }
        }
    }
}
