pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                // Clone Repository
                script {
                    echo 'Cloning GitHub Repository...'
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'mlops-git-token', url: 'https://github.com/Ziadhammad14/MLOps01.git']])
                }
            }
        }
        stage('Lint Code') {
            steps {
        // Lint code
                script {
                    echo 'Linting Python Code...'
                    sh "python3 --version"
                    //sh "python -m pip install --break-system-packages -r requirements.txt"
                    //sh "pylint app.py train.py --output=pylint-report.txt --exit-zero"
                    //sh "flake8 app.py train.py --ignore=E501,E302 --output-file=flake8-report.txt"
                    //sh "black app.py train.py"
            
        }
    }
}
        stage('Test Code') {
            steps {
                // Pytest code
                script {
                    echo 'Testing Python Code...'
                    // sh "pytest tests/"
                }
            }
        }
        stage('Trivy FS Scan') {
            steps {
                // Trivy Filesystem Scan
                script {
                    echo 'Scannning Filesystem with Trivy...'
                    // sh "trivy fs ./ --format table -o trivy-fs-report.html"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker Image
                script {
                    echo 'Building Docker Image...'
                    // dockerImage = docker.build("${DOCKERHUB_REPOSITORY}:latest") 
                }
            }
        }
        stage('Trivy Docker Image Scan') {
            steps {
                // Trivy Docker Image Scan
                script {
                    echo 'Scanning Docker Image with Trivy...'
                    // sh "trivy image ${DOCKERHUB_REPOSITORY}:latest --format table -o trivy-image-report.html"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                // Push Docker Image to DockerHub
                script {
                    echo 'Pushing Docker Image to DockerHub...'
                    // docker.withRegistry("${DOCKERHUB_REGISTRY}", "${DOCKERHUB_CREDENTIAL_ID}"){
                        //dockerImage.push('latest')
                    }
                }
            }
        
        stage('Deploy') {
            steps {
                // Deploy Image to Amazon ECS
                script {
                    echo 'Deploying to production...'
                        // sh "aws ecs update-service --cluster iquant-ecs --service iquant-ecs-svc --force-new-deployment"
                    }
                }
            }
        }
    }