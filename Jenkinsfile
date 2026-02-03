pipeline {
    agent any

    tools {
        jdk 'JDK21'
    }

    environment {
        AWS_REGION = 'ap-south-1'
        CLUSTER    = 'ecs-cluster'
        SERVICE    = 'employee-task-service-pio1exln'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    credentialsId: 'github-cred',
                    url: 'https://github.com/venkipesala/Employee.git'
            }
        }

        stage('Build & Push Image (Jib → ECR)') {
            steps {
                sh '''
                  echo "Building & Pushing Image to ECR..."

                  mvn clean compile jib:build
                '''
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                  echo "Deploying to ECS..."

                  aws ecs update-service \
                    --cluster $CLUSTER \
                    --service $SERVICE \
                    --force-new-deployment \
                    --region $AWS_REGION
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployed Successfully to ECS 🚀'
        }

        failure {
            echo '❌ Deployment Failed'
        }
    }
}
