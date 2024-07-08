pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: 'https://github.com/MarcoCoppola135/Gallery.git'
            }
        }

        stage('Check Python and Pip Installation') {
            steps {
                script {
                    powershell '''
                        python --version
                        pip --version
                    '''
                }
            }
        }
        
        stage('Install dependencies') {
            steps {
                script {
                    powershell '''
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    powershell 'docker build -t myapp:latest .'
                }
            }
        }

        stage('Run Docker container') {
            steps {
                script {
                    powershell '''
                        if (docker ps -q --filter "name=myapp_container") {
                            docker stop myapp_container
                        }
                        if (docker ps -aq --filter "name=myapp_container") {
                            docker rm myapp_container
                        }
                        docker run -d -p 5000:5000 --name myapp_container myapp:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            script {
                powershell '''
                    if (docker ps -q --filter "name=myapp_container") {
                        docker stop myapp_container
                    }
                    if (docker ps -aq --filter "name=myapp_container") {
                        docker rm myapp_container
                    }
                '''
            }
        }
    }
}
