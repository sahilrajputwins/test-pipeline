pipeline{
    agent any
    environment {
        container_name= 'test-container'
    }
    stages{
        stage("Build") {
            steps {
                bat 'docker build -t sahilrajputwins/test-pipeline:%BUILD_ID% .'
            }
        }

        stage("Test") {
            steps {
                script{
                    def containerExists = bat(script: 'docker ps -a --format "{{.Names}}" | findstr %container_name%', returnStatus: true)==0

                    if (containerExists) {
                        echo "Stopping and removing existing container..."
                        bat 'docker stop %container_name%'
                        bat 'docker rm %container_name%'
                    } else {
                        echo "No existing container found, running a new one..."
                    }  
                    bat 'docker run --name %container_name% -p 80:80 -d sahilrajputwins/test-pipeline:%BUILD_ID%'
                }   
            }
        }
        stage("Login") {
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]){
                        bat '''echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin'''
                    }
                }
                
            }
        }
        stage("Package") {
            steps {
                bat 'docker push sahilrajputwins/test-pipeline:%BUILD_ID%' 
            }
        }
        stage("Deployed") {
            steps{
                bat '''echo deployed, build id: %BUILD_ID%'''
            }
        }
    }
}