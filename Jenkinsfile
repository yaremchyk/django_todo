pipeline {
    agent { node { label 'ci-server' } }
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    

    
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/yaremchyk/django_todo.git']])
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
                        sh 'docker build -t dev/demo3 .'
                        sh 'docker tag dev/demo3:latest 025389115636.dkr.ecr.eu-north-1.amazonaws.com/dev/demo3:latest' 
            }
        }
        stage("Pushing to ECR"){
            steps{
                    echo "Pushing to ECR..."
                    sh "docker push 025389115636.dkr.ecr.eu-north-1.amazonaws.com/dev/demo3:latest"
            }
        }
        stage('Update EKS'){
            steps{
                sh "aws eks update-kubeconfig --name demo-cluster"
                sh "kubectl rollout restart deployment todo-deployment"
            }
        }
        stage('Clear space'){
            steps{
                sh 'docker system prune --all --force --volumes'
            }
        }
        
    }
}
