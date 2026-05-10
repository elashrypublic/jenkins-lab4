pipeline {
    agent { label 'node-agent' }

    environment {
        AWS_REGION = 'us-east-1'
        ECR_URI = '398681517866.dkr.ecr.us-east-1.amazonaws.com/ashry-jenkins-lab'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Install & Test') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"

                    npm install
                    npm test
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t $ECR_URI:$IMAGE_TAG .
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-ecr-creds'
                ]]) {

                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin \
                        398681517866.dkr.ecr.us-east-1.amazonaws.com

                        docker push $ECR_URI:$IMAGE_TAG
                    '''
                }
            }
        }
    }
}