pipeline {
    agent any

    environment {
        PYTHON_VERSION = "3.10"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                sh '''
                sudo apt-get update
                sudo apt-get install -y python3.10 python3.10-venv python3.10-distutils
                python3.10 -m venv venv
                . venv/bin/activate
                python -m pip install --upgrade pip
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                . venv/bin/activate
                if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                pip install ruff pytest coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                sh '''
                . venv/bin/activate
                ruff --format=github --target-version=py310 . || true
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                coverage run -m pytest -v -s
                '''
            }
        }

        stage('Coverage Report') {
            steps {
                sh '''
                . venv/bin/activate
                coverage report -m
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}