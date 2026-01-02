pipeline {
    agent any

    environment {
        NODE_MODULES_CACHE = "${WORKSPACE}/frontend/node_modules"
        VENV_PATH = "${WORKSPACE}\\Backend\\venv"
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    credentialsId: 'github-tokens',
                    url: 'https://github.com/Shakthimala/health_and_fitness_app.git'
                )
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    bat 'cd Backend && if not exist venv (python -m venv venv)'
                    bat 'cd Backend && call venv\\Scripts\\activate.bat && pip install -r requirements.txt'
                    bat 'cd Backend && call venv\\Scripts\\activate.bat && pip show mongomock_motor || pip install mongomock_motor'
                    bat '''
                    cd frontend
                    if not exist node_modules (
                        echo "node_modules not found. Installing dependencies..."
                        npm install
                    ) else (
                        echo "Using cached node_modules"
                    )
                    '''
                    bat 'cd frontend && npm list @heroicons/react || npm install @heroicons/react'
                    bat 'cd frontend && npm list workbox-cli || npm install workbox-cli'
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                script {
                    bat 'cd frontend && npm run build'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running Tests...'
                script {
                    withCredentials([string(credentialsId: 'google-client-id', variable: 'GOOGLE_CLIENT_ID')]) {
                        bat 'cd Backend && call venv\\Scripts\\activate.bat && pytest test || exit /b 1'
                        bat 'cd frontend && npm test || exit /b 1'
                    }
                }
            }
        }

        stage('Build PWA') {
            steps {
                echo 'Building PWA features for innovation...'
                script {
                    bat 'cd frontend && npm run build-pwa'
                }
            }
        }

        stage('Verify Installation') {
            steps {
                echo 'Verifying @heroicons/react installation...'
                script {
                    bat 'cd frontend && npm list @heroicons/react'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                script {
                    bat 'echo Deployment script goes here'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for more details.'
        }
    }
}