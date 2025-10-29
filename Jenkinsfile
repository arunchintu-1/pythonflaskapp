pipeline {
    agent any

    environment {
        
        DOCKER_IMAGE = "arunchintu/pythonflaskapp"
    }

    options {
        skipStagesAfterUnstable()  // stop after failure
        timestamps()               // add timestamps in logs
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📦 Cloning the repository...'
                git branch: 'main', url: 'https://github.com/arunchintu-1/pythonflaskapp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '⚙️ Setting up Python environment...'
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
                echo '🧪 Running unit tests...'
                sh '''
                . venv/bin/activate
                pytest --maxfail=1 --disable-warnings -q || true
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh '''
                docker build -t $DOCKER_IMAGE:latest .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo '🚀 Pushing image to DockerHub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-token', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE:latest
                    docker logout
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                echo '🚢 Deploying container locally...'
                sh '''
                docker rm -f flask-container || true
                docker run -d -p 5000:5000 --name flask-container $DOCKER_IMAGE:latest
                '''
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up workspace...'
            deleteDir()
        }
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Build failed. Check Jenkins logs for details.'
        }
    }
}
