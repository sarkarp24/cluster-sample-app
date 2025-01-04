pipeline {
    agent any
    environment {
        REACT_APP_VERSION = "latest"
        AWS_DEFAULT_REGION = 'us-west-2'
        IMAGE_REG = "docker.io"
        IMAGE_REPO = "prodyotsarkar/python-demoapp"
        IMAGE_TAG = "latest"
        AWS_APP_NAME = "cluster-sample-app"
        AWS_REGISTRY = '762233725823.dkr.ecr.us-west-2.amazonaws.com'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git changelog: false, poll: false, url: 'https://github.com/sarkarp24/cluster-sample-app.git'
                sh 'docker --version'
            }
        }
        stage('Build Docker image') {
            agent {
                docker {
                    image 'my-aws-cli'
                    reuseNode true
                    args "-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=''"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'Prodyot-DhongDhong', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        docker build -t $AWS_REGISTRY/$AWS_APP_NAME:$REACT_APP_VERSION .
                        aws ecr get-login-password | docker login --username AWS --password-stdin $AWS_REGISTRY
                        aws ecr create-repository --repository-name $AWS_APP_NAME
                        docker push $AWS_REGISTRY/$AWS_APP_NAME:$REACT_APP_VERSION
                    '''
                }
            }
        }
    }
}
