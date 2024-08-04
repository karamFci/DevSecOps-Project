pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_HUB_REPO = 'karamfci/mh-repo'
        KUBECONFIG_CREDENTIALS = credentials('kubeconfig-credentials-id')
        TMDB_V3_API_KEY = '0bc77662769bd42eb21db4f3262dc344'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/karamFci/DevSecOps-Project.git'
            }
        }
        
        stage('Build') {
            steps {
                script {
                    docker.build("netflix:latest --build-arg TMDB_V3_API_KEY=${env.TMDB_V3_API_KEY} .")
                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub') {
                        def app = docker.image("netflix:latest")
                        app.push()
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig-credentials-id', variable: 'KUBECONFIG')]) {
                        sh 'kubectl apply -f k8s/deployment.yaml -n solution --kubeconfig=$KUBECONFIG'
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}