pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIAL_ID = 'mlops-jenkins-dockerhub-token'
        DOCKERHUB_REGISTRY = 'https://registry.hub.docker.com'
        DOCKERHUB_REPOSITORY = 'iquantc/mlops-proj-01'
    }
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
        script {
            echo 'Linting Python Code...'
            sh '''
                # Add universe repository (Ubuntu) or non-free (Debian)
                apt-get update && apt-get install -y software-properties-common
                # add-apt-repository universe  # For Ubuntu
                add-apt-repository non-free  # For Debian
                apt-get update
                
                # Now try installing again
                apt-get install -y python3-pip python3-venv
                
                # Continue with virtual environment approach
                python3 -m venv /tmp/venv
                . /tmp/venv/bin/activate
                pip install pylint flake8 black
                # ... rest of your linter commands
            '''
        }
    }
}
        stage('Test Code') {
            steps {
                // Pytest code
                script {
                    echo 'Testing Python Code...'
                    sh '''
                         # Activate the virtual environment we created earlier
                        . /tmp/venv/bin/activate
                
                        # Clean installation of required packages
                        pip uninstall -y pytest-cov || true
                        pip install --no-cache-dir pytest pytest-cov
                
                        # Verify the installation
                        python -c "import pytest_cov; print(f'pytest-cov version: {pytest_cov.__version__}')"
                
                        # Run tests with coverage and JUnit reporting
                        python -m pytest tests/ \
                        --junitxml=test-results.xml \
                        --cov=./ \
                        --cov-report=xml:coverage.xml
                    '''
            }
            post {
                always {
                junit 'test-results.xml'
                cobertura 'coverage.xml'
                archiveArtifacts artifacts: 'coverage.xml', fingerprint: true    
                    }
                }
            }
        }
        stage('Trivy FS Scan') {
            steps {
                // Trivy Filesystem Scan
                script {
                    echo 'Scannning Filesystem with Trivy...'
                    sh "trivy fs ./ --format table -o trivy-fs-report.html"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker Image
                script {
                    echo 'Building Docker Image...'
                    dockerImage = docker.build("${DOCKERHUB_REPOSITORY}:latest") 
                }
            }
        }
        stage('Trivy Docker Image Scan') {
            steps {
                // Trivy Docker Image Scan
                script {
                    echo 'Scanning Docker Image with Trivy...'
                    sh "trivy image ${DOCKERHUB_REPOSITORY}:latest --format table -o trivy-image-report.html"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                // Push Docker Image to DockerHub
                script {
                    echo 'Pushing Docker Image to DockerHub...'
                    docker.withRegistry("${DOCKERHUB_REGISTRY}", "${DOCKERHUB_CREDENTIAL_ID}"){
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                // Deploy Image to Amazon ECS
                script {
                    echo 'Deploying to production...'
                        sh "aws ecs update-service --cluster iquant-ecs --service iquant-ecs-svc --force-new-deployment"
                    }
                }
            }
        }
    }
