pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: 'https://github.com/MarcoCoppola135/Gallery.git'
            }
        }
        
        stage('Install dependencies') {
            steps {
                script {
                    powershell '''
                        $env:Path += "C:/Users/leona/AppData/Local/Programs/Python/Python311"
                        python -m venv venv
                        .\\venv\\Scripts\\Activate.ps1
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    powershell '''
                        if (docker images -q myapp) {
                            docker rm myapp
                        }
                        docker build -t myapp:latest .
                    '''
                    
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
                        docker run -d -p 3000:3000 --name myapp_container myapp:latest
                    '''
                }
            }
        }
        
        stage('Remove Docker images') {
            steps {
                script {
                    powershell '''
                        if (docker images -f "dangling=true" -q) {
                            docker image prune -f
                        }
                    '''        
    }
}
