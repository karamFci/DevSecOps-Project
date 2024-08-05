pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_HUB_REPO = 'karamfci/mh-repo'
        KUBECONFIG_CREDENTIALS = credentials('kubeconfig-credentials-id')
        TMDB_V3_API_KEY = '0bc77662769bd42eb21db4f3262dc344'
    }
    
    stages {

        
        stage("Fix the permission issue") {

            agent any

            steps {
                sh "sudo chown root:docker /run/docker.sock"
            }

        }
        stage('Checkout') {
            steps {
                git branch: 'main' , url: 'https://github.com/karamFci/DevSecOps-Project.git'

            }
        }
        
        stage('Build') {
            steps {
                script {
                    def netImage = docker.build("netflix", "--build-arg TMDB_V3_API_KEY=${env.TMDB_V3_API_KEY} .") 

                   // docker.build("netflix:latest" "--build-arg TMDB_V3_API_KEY=${env.TMDB_V3_API_KEY} .")
                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        def app = docker.image("netflix:latest")
                        docker.image('netflix').push('latest')
                        // app.push("latest")
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig-credentials-id', variable: 'KUBECONFIG')]) {
                        sh 'kubectl apply -f deployment.yaml -n solution --kubeconfig=$KUBECONFIG'
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
