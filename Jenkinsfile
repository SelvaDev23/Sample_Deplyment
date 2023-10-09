pipeline {
    agent {
      docker {
          image 'nginx:latest'
            args '-p 80:80'
            }
        }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/SelvaDev23/Sample_Deplyment.git']]])
            }
        }
        stage('Build and Push Docker Image') {
            steps {
                script {
                    def My-app = "my-nginx-app:${env.BUILD_NUMBER}"

                    // Build the NGINX Docker image
                    sh "docker build -t $my-nginx-app ."

                    // Push the Docker image to a container registry (e.g., Docker Hub)
                    sh "docker push $my-nginx-app"
                }
            }
        }

        stage('Deploy NGINX') {
            steps {
                // Run the NGINX container from the custom image
                sh "docker run -d -p 80:80 $my-nginx-app"
            }
        }

        stage('Cleanup') {
            steps {
                // Clean up Docker resources
                sh "docker stop $(docker ps -q --filter ancestor=$my-nginx-app)"
                sh "docker rm $(docker ps -aq --filter ancestor=$my-nginx-app)"
                sh "docker rmi $my-nginx-app"
            }
        }
    }

    post {
        always {
            // Clean up any remaining Docker resources on Jenkins agent
            cleanWs()
        }
    }
