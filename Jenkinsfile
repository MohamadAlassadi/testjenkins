pipeline {
    agent any

    triggers {
        // تنفيذ تلقائي عند وجود push جديد (كل دقيقتين تحقق)
        pollSCM('H/2 * * * *')
    }

    environment {
        APP_NAME = "testjenkins"
        DEPLOY_DIR = "/tmp/${APP_NAME}"
        VENV = "venv"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📥 Cloning repository..."
                git branch: 'main', url: 'https://github.com/MohamadAlassadi/testjenkins.git'
            }
        }

        stage('Setup Environment') {
            steps {
                echo "⚙️ Setting up Python virtual environment..."
                sh '''
                    python3 -m venv ${VENV}
                    . ${VENV}/bin/activate
                    pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                '''
            }
        }

        stage('Build') {
            steps {
                echo "🏗️ Building the Python project..."
                sh '''
                    . ${VENV}/bin/activate
                    python3 -m py_compile jenkins.py
                '''
            }
        }

        stage('Test') {
            steps {
                echo "🧪 Running tests..."
                sh '''
                    . ${VENV}/bin/activate
                    python3 jenkins.py | grep -q "Hello" && echo "✅ Test Passed" || (echo "❌ Test Failed"; exit 1)
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying application to ${DEPLOY_DIR}"
                sh '''
                    mkdir -p ${DEPLOY_DIR}
                    cp jenkins.py ${DEPLOY_DIR}/
                    echo "Deployment complete to ${DEPLOY_DIR}"
                '''
            }
        }
    }

    post {
        success {
            echo "✅ All stages completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Please check the logs."
        }
    }
}
