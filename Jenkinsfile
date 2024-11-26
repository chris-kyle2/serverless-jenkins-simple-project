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
        stage('Test') {
            steps {
                sh '''
                cd lambda-app/tests
                pytest
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