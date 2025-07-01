pipeline {
    agent any

    environment {
        // Define any environment variables here
        AWS_REGION = 'ap-aouth-1'
        IMAGE_NAME = 'todo-backend'
        ECR_REGISTRY = '476287788215.dkr.ecr.${AWS_REGION}.amazonaws.com'
        ECR_REPO = "${ECR_REGISTRY}/${IMAGE_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                git branch: 'release-dev', url: 'https://github.com/aws-devops-2-0/todo-backend.git' 
                // Add your checkout commands here, e.g., git checkout
            }
        }
        stage('Build JAR') {
            steps {
                echo 'Building JAR...'
                // Add your build commands here
                sh './mvnw clean package -DskipTests'
            }
        }
        stage('Docker Build & Push') {
            steps {
                echo 'Building and pushing Docker image...'
                // Add your deployment commands here
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY
                    docker build -t $IMAGE_NAME:latest .
                    docker tag $IMAGE_NAME:latest $ECR_REPO:latest
                    docker push $ECR_REPO:latest
                    '''
            }
        }
    }    
}