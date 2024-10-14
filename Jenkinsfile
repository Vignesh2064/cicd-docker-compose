pipeline {
    agent any

    stages {
        stage('Cleanup Workspace') {
            steps {
                // Clean up the workspace to avoid conflicts or leftover files from previous builds
                cleanWs()
            }
        }

        stage('Checkout Repositories') {
            steps {
                script {
                    // Checkout the first repository from GitHub
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/Vignesh2064/cicd-docker-compose.git'

                    // Checkout the second repository
                    dir('node-todo-cicd') {  // Change to a new directory for the second repo
                        git branch: 'master', credentialsId: 'github', url: 'https://github.com/Vignesh2064/node-todo-cicd.git'
                    }
                }
            }
        }

        stage('Remove All Docker Services') {
            steps {
                script {
                    // Remove all services in the swarm
                    sh """
                        echo "Removing all Docker services..."
                        docker service rm \$(docker service ls -q) || true  # Remove all services
                    """
                }
            }
        }

        stage('Run Docker Compose') {
            steps {
                script {
                    // Ensure Docker Swarm is initialized (optional)
                    sh """
                        docker swarm init || true  # Initialize Swarm, ignore error if already initialized
                    """

                    // Use docker stack to deploy the services in detached mode with the correct filename
                    sh """
                        docker stack deploy -c docker-compose.yaml app-deployment  # Deploy the stack using the correct file
                        echo "Services are running in detached mode."
                    """
                }
            }
        }
    }
}
