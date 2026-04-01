pipeline {
    agent any

    parameters {
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run test stage?')
    }

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
                python3 --version
                python3 -m venv venv
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
            when {
                expression { params.RUN_TESTS == true }
            }
            steps {
                sh '''
                . venv/bin/activate
                coverage run -m pytest -v -s
                '''
            }
        }

        stage('Coverage Report') {
            when {
                expression { params.RUN_TESTS == true }
            }
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