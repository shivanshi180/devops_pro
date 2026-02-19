pipeline {
    agent any

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install fastapi uvicorn
                '''
            }
        }

        stage('Run App') {
            steps {
                sh '''
                    . venv/bin/activate
                    echo "Starting FastAPI..."
                    uvicorn main:app --host 0.0.0.0 --port 8000 &
                '''
            }
        }
    }
}
