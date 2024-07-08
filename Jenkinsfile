pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-jenkins'
        CONTAINER_NAME = 'jenkins'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    powershell '''
                    docker build -t $env.DOCKER_IMAGE .
                    '''
                }
            }
        }

        stage('Run Jenkins Container') {
            steps {
                script {
                    // Stop and remove the container if it already exists
                    powershell '''
                    if (docker ps -a -q -f name=$env.CONTAINER_NAME) {
                        docker stop $env.CONTAINER_NAME
                        docker rm $env.CONTAINER_NAME
                    }
                    # Run the Jenkins container
                    docker run -d -p 8080:8080 -p 50000:50000 --name $env.CONTAINER_NAME $env.DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Setup Jenkins') {
            steps {
                script {
                    // Wait for Jenkins to start up
                    powershell 'Start-Sleep -Seconds 30'

                    // Print the initial admin password
                    powershell '''
                    docker exec $env.CONTAINER_NAME powershell -command "Get-Content -Path 'C:\\ProgramData\\Jenkins\\.jenkins\\secrets\\initialAdminPassword'"
                    '''
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    // Stop and remove the Jenkins container after the pipeline is done
                    powershell '''
                    docker stop $env.CONTAINER_NAME
                    docker rm $env.CONTAINER_NAME
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up Docker images to free space
                powershell '''
                docker rmi $env.DOCKER_IMAGE || Write-Host "Failed to remove image"
                '''
            }
        }
    }
}
