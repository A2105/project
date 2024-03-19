pipeline {
    agent any

    environment {
        // Set your AWS credentials ID configured in Jenkins
        AWS_CREDENTIALS_ID = 'awsid'
        // Set your EC2 instance details
        EC2_INSTANCE_IP = '13.54.240.31'
        // Set the Docker image name
        DOCKER_IMAGE_NAME = 'mynewapp'
    }

    stages {
        stage('Checkout') {
            steps {
                // Pull the latest changes from the GitHub repository
                git 'https://github.com/A2105/project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image using the Dockerfile and updated cloud.html
                script {
                    docker.build("${env.DOCKER_IMAGE_NAME}", "-f Dockerfile .")
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                // Use SCP to copy the Docker image to the EC2 instance and run it
                sh "scp -o StrictHostKeyChecking=no ${env.DOCKER_IMAGE_NAME} ec2-user@${env.EC2_INSTANCE_IP}:"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${env.EC2_INSTANCE_IP} sudo docker stop \$(sudo docker ps -aq)"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${env.EC2_INSTANCE_IP} sudo docker rm \$(sudo docker ps -aq)"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${env.EC2_INSTANCE_IP} sudo docker run -d -p 8075:80 ${env.DOCKER_IMAGE_NAME}"
            }
        }
    }
}

