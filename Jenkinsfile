pipeline {
    agent none
    environment {
        // Define environment variables
        DOCKER_NAMESPACE = 'toolboxplayground'
        DOCKER_REPOSITORY = 'nodejs-jenkins'
        DOCKER_TAG = 'latest'
        DOCKER_CREDS = credentials('toolboxdocker')
    }
    stages { // Define the stages of the pipeline
        stage('Install') { // Define the stage Install
            agent { // Define the agent for the stage Install (Docker)
                docker { // Define the Docker agent
                    image 'node:alpine' // Define the Docker image
                }
            }
            steps { // Define the steps of the stage Install
                sh 'cd app && npm install' // Execute the command to install the dependencies
            }
        }
        stage('Test') { // Define the stage Test
            agent { // Define the agent for the stage Test (Docker)
                docker { // Define the Docker agent
                    image 'node:alpine' // Define the Docker image
                }
            }
            steps { // Define the steps of the stage Test
                sh 'cd app && npm test' // Execute the command to run the tests
            }
        }
        stage('Build Docker Image') { // Define the stage Build Docker Image
            agent any // Define the agent for the stage Build Docker Image (Any)
            steps { // Define the steps of the stage Build Docker Image
                script { // Define the script to execute
                    // Build the Docker image
                    sh "docker build -t ${env.DOCKER_NAMESPACE}/${env.DOCKER_REPOSITORY}:${env.DOCKER_TAG} ." // Execute the command to build the Docker image
                }
            }
        }
        stage('Push Docker Image') { // Define the stage Push Docker Image
            agent any // Define the agent for the stage Push Docker Image (Any)
            steps { // Define the steps of the stage Push Docker Image
                script { // Define the script to execute
                    // Log in to Docker Hub and push the Docker image
                    sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin' // Execute the command to log in to Docker Hub
                    sh "docker push ${env.DOCKER_NAMESPACE}/${env.DOCKER_REPOSITORY}:${env.DOCKER_TAG}" // Execute the command to push the Docker image
                    sh "docker logout" // Execute the command to log out from Docker Hub
                }
            }
        }
        stage('Pull ant Test Docker Image') { // Define the stage Pull and Test Docker Image
            agent any // Define the agent for the stage Pull and Test Docker Image (Any)
            steps { // Define the steps of the stage Pull and Test Docker Image
                script { // Define the script to execute
                    // Log in to Docker Hub and push the Docker image
                    sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin' // Execute the command to log in to Docker Hub
                    sh "docker pull ${env.DOCKER_NAMESPACE}/${env.DOCKER_REPOSITORY}:${env.DOCKER_TAG}" // Execute the command to pull the Docker image
                    // Select a port to test the application that is not the same as the one used by Jenkins and not the same as the one used by others applications
                    sh "docker run -d -p 8081:8080 --name jenkins-test --rm ${env.DOCKER_NAMESPACE}/${env.DOCKER_REPOSITORY}:${env.DOCKER_TAG}" // Execute the command to run the Docker image
                    sh "sleep 10" // Execute the command to wait for the application to start
                    sh "docker ps -f 'name=jenkins-test'" // Execute the command to check if the Docker container is running
                    sh "docker rm -f jenkins-test" // Execute the command to stop the Docker container
                    sh "docker logout" // Execute the command to log out from Docker Hub
                }
            }
        }
    }
}
