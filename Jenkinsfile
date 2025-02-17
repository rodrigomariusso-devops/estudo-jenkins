pipeline {
    agent any 
    
    stages {
        stage("Clone Code") {
            steps {
                echo "Cloning the code"
                git url: "https://github.com/rodrigomariusso-devops/estudo-jenkins.git", branch: "main"
            }
        }

        stage("Build") {
            steps {
                echo "Building the image"
                sh "docker build -t jenkins-deploy ."
            }
        }

        stage("Push to AWS ECR") {
            steps {
                echo "Pushing the image to AWS ECR"
                withCredentials([aws(credentialsId: "aws-ecr-credentials", region: "us-east-1")]) {
                    script {
                        def awsAccountId = "598723879413" // Substitua pelo seu AWS Account ID
                        def region = "us-east-2" // Substitua pela região do seu ECR
                        def repositoryName = "jenkins-deploy"
                        def ecrUri = "${awsAccountId}.dkr.ecr.${region}.amazonaws.com/${repositoryName}"

                        // Login no ECR
                        sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${awsAccountId}.dkr.ecr.${region}.amazonaws.com"

                        // Taguear a imagem
                        sh "docker tag jenkins-deploy ${ecrUri}:latest"

                        // Enviar para o ECR
                        sh "docker push ${ecrUri}:latest"
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the container"
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
