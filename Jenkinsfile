pipeline {
    agent any
    options { skipDefaultCheckout() }

    
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/chris-kyle2/serverless-jenkins-simple-project.git', branch: 'main'
                sh "echo 'Successfully checked out repository' "
                sh "ls -ltr"
            }
        }

        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    cd lambda-app/tests
                    pip install -r requirements.txt
                   '''
            }
        }
        stage('Install AWS SAM CLI') {
            steps {
                sh '''
                # Update the system
                sudo apt update -y
                
                # Install prerequisites
                sudo apt install -y python3 python3-pip unzip
                
                # Download the AWS SAM CLI
                wget https://github.com/aws/aws-sam-cli/releases/latest/download/aws-sam-cli-linux-x86_64.zip
                
                # Unzip the installation package
                unzip aws-sam-cli-linux-x86_64.zip -d sam-installation
                
                # Move to a directory in PATH
                sudo mv sam-installation /usr/local/aws-sam-cli
                sudo ln -s /usr/local/aws-sam-cli/sam /usr/bin/sam
                
                # Verify the installation
                sam --version
                '''
            }
        }
        

        stage('Build') {
            steps {
                sh "sam build -t lambda-app/template.yaml"

            }
        }
        stage('Deploy') {

            environment {
                AWS_ACCESS_KEY_ID = credentials('aws-access-key')
                AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
            }
            
            steps {
                sh "sam deploy -t lambda-app/template.yaml --no-confirm-changeset --no-fail-on-empty-changeset"

            }
        }
      
    }
}