pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        APP_NAME = "testjenkins"
        DEPLOY_DIR = "C:\\tmp\\${APP_NAME}"
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
                bat '''
                    python -m venv %VENV%
                    call %VENV%\\Scripts\\activate
                    python -m pip install --upgrade pip
                    if exist requirements.txt (pip install -r requirements.txt)
                '''
            }
        }

       stage('Build') {
    steps {
        echo "🏗️ Building the Python project..."
        bat '''
            call venv\\Scripts\\activate
            python -m py_compile jenkins.py
            if %errorlevel% neq 0 exit /b %errorlevel%
        '''
    }
}

stage('Test') {
    steps {
        echo "🧪 Running tests..."
        bat '''
            call venv\\Scripts\\activate
            python jenkins.py > output.txt
            findstr "Hello" output.txt
            if %errorlevel% neq 0 exit /b %errorlevel%
        '''
    }
}

stage('Deploy') {
    steps {
        echo "🚀 Deploying ${APP_NAME}..."
        bat '''
            if not exist %DEPLOY_DIR% mkdir %DEPLOY_DIR%
            copy jenkins.py %DEPLOY_DIR%
            echo Application deployed to %DEPLOY_DIR%
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
