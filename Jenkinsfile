pipeline {
    agent any

    environment {
        PYTHON_VERSION = "3.10"
        VENV = "${WORKSPACE}/venv"
    }

    stages {

        stage('Setup Python') {
            steps {
                sh '''
                python3 --version
                python3 -m venv venv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                withEnv(["PATH=${VENV}/bin:$PATH"]) {
                    sh '''
                    python -m pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    pip install ruff pytest coverage
                    '''
                }
            }
        }

        stage('Lint with Ruff') {
            steps {
                withEnv(["PATH=${VENV}/bin:$PATH"]) {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh 'ruff check . --output-format=github --target-version=py310'
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                withEnv(["PATH=${VENV}/bin:$PATH"]) {
                    sh 'coverage run -m pytest -v -s'
                }
            }
        }

        stage('Coverage Report') {
            steps {
                withEnv(["PATH=${VENV}/bin:$PATH"]) {
                    sh 'coverage report -m'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo 'Build SUCCESS'
        }
        failure {
            echo 'Build FAILED'
        }
    }
}