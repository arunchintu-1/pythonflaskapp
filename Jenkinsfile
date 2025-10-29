pipeline {
    agent any

    environment {
        APP_NAME = "flask-app"
        DOCKER_IMAGE = "flask-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Cloning repository..."
                git 'https://github.com/arunchintu-1/pythonflaskapp.git'
            }
        }

        stage('Setup Python & Install Dependencies') {
            steps {
                echo "🐍 Setting up Python environment..."
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo "🧪 Running unit tests..."
                sh '''
                . venv/bin/activate
                PYTHONPATH=. pytest -q || exit 1
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Docker Container') {
            steps {
                echo "🚀 Running container..."
                sh '''
                docker run -d -p 5000:5000 --name flask-container $DOCKER_IMAGE
                sleep 5
                docker ps
                '''
            }
        }

        stage('Deploy (Optional)') {
            steps {
                echo "🛠️ Deployment can be to EKS, EC2, or Docker Host"
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning up containers..."
            sh '''
            docker stop flask-container || true
            docker rm flask-container || true
            '''
        }
    }
}
