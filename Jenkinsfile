pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        SONAR_PROJECT = "devops_pro"
        SONAR_URL = "http://172.31.32.123:9000" 
        PROJECT_LANG = "python"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                credentialsId: '49acb13a-3b47-47d6-984e-01bb0100745b',
                url: 'https://github.com/shivanshi180/devops_pro.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    rm -rf venv
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }
//
//        stage('SonarQube Scan') {
//            steps {
//                withCredentials([string(credentialsId: 'sonar-token-shivanshi', variable: 'SONAR_TOKEN')]) {
//                    sh """
//                    echo Token length: \${#SONAR_TOKEN}
//                    sonar-scanner \
//                    -Dsonar.projectKey=sonarqube_demo_pro \
//                    -Dsonar.sources=. \
//                    -Dsonar.host.url=http://ec2-13-202-47-19.ap-south-1.compute.amazonaws.com:15998 \
//                    -Dsonar.login=$SONAR_TOKEN
//                    """
//                }
//            }
//        }

        stage('Snyk Scan') {
            steps {
                withCredentials([string(credentialsId: 'snyk-token-shivanshi', variable: 'SNYK_TOKEN')]) {
                    sh '''
                    . venv/bin/activate
                    snyk auth $SNYK_TOKEN
                    snyk test --all-projects 
                    snyk monitor --all-projects 
                    '''
                }
            }
        }

        stage('Run App') {
            steps {
                sh '''
                    echo "Stopping old app..."
                    pkill -f "uvicorn main:app" || true

                    echo "Starting FastAPI..."
                    nohup venv/bin/python -m uvicorn main:app \
                    --host 0.0.0.0 \
                    --port 8000 \
                    > app.log 2>&1 &

                    sleep 5
                    echo "App started successfully"
                '''
            }
        }

        stage('DAST (OWASP ZAP Scan)') {
            steps {
                sh '''
                echo "Starting ZAP Scan..."

                ~/ZAP_*/zap.sh -cmd \
                -quickurl http://127.0.0.1:8000 \
                -quickout zap_report.html \
                -quickprogress

                echo "ZAP Scan Completed"
                '''
            }

            post {
                always {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: '.',
                        reportFiles: 'zap_report.html',
                        reportName: 'ZAP DAST Scan Report'
                    ])
                }
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                venv/bin/pip install build
                venv/bin/python -m build
                '''
            }
        }
    }
}