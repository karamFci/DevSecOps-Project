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

                    dockerImage = docker.build ("karamfci/mh-repo" , "--build-arg TMDB_V3_API_KEY=${env.TMDB_V3_API_KEY} .")   

                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub') {
                        dockerImage.push("${env.BUILD_NUMBER}")

                    }
                }
            }
        }
        

        stage('Trigger ManifestUpdate') {
            steps {
                script {
                    echo "triggering updatemanifestjob"
                    build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
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
