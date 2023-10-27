pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/yaremchyk/DevOps_SS_Demo2.git']])
            }
        }
        stage("ECR Authentication"){
            steps{
                echo "Logging to ECR.."
                sh "aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin 025389115636.dkr.ecr.eu-north-1.amazonaws.com"
                echo "--------------------------OK--------------------------"
            }
        }
   
        stage('Build&Tag') {
            steps {           
                        sh 'docker build -t app-repo .'
                        sh 'docker tag app-repo:latest 025389115636.dkr.ecr.eu-north-1.amazonaws.com/app-repo:latest' 
            }
        }
        stage("Pushing to ECR"){
            steps{
                    echo "Pushing to ECR..."
                    sh "docker push 025389115636.dkr.ecr.eu-north-1.amazonaws.com/app-repo:latest"
            }
        }
        stage('Update ECS'){
            steps{
                sh 'aws ecs update-service --region eu-north-1 --cluster app-cluster --service demo-2-service --force-new-deployment'
            }
        }
        
    }
}
