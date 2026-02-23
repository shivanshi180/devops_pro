pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: '49acb13a-3b47-47d6-984e-01bb0100745b', url: 'https://github.com/shivanshi180/devops_pro.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    venv/bin/python -m ensurepip --upgrade
                    ./venv/bin/pip install --upgrade pip
                    ./venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Run App') {
            steps {
                sh '''
                    echo "Starting FastAPI..."
                    venv/bin/python -m uvicorn main:app --host 0.0.0.0 --port 8000
                '''
            }
        }
    }
}