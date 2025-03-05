pipeline {
    agent any
    
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'], description: 'Deployment environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run automated tests')
        booleanParam(name: 'RUN_SECURITY_SCAN', defaultValue: true, description: 'Run security scan')
        string(name: 'BRANCH', defaultValue: 'main', description: 'Branch to build')
    }
    
    environment {
        GITHUB_REPO = 'https://github.com/fatidiop/buy-01.git'
        JAVA_HOME = tool 'JDK11'
        MAVEN_HOME = tool 'Maven3'
        NODEJS_HOME = tool 'NodeJS'
        PATH = "${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.NODEJS_HOME}/bin:${env.PATH}"
        DOCKER_REGISTRY = 'registry.example.com'  // Remplacez par votre registre Docker
        DOCKER_CREDENTIALS = 'docker-credentials'  // Remplacez par vos credentials Docker
    }
    
    stages {
        // Étape de checkout commune
        stage('Checkout') {
            steps {
                git branch: params.BRANCH, 
                    url: env.GITHUB_REPO, 
                    credentialsId: 'github-credentials'
            }
        }
        
        // Frontend
        stage('Frontend Install Dependencies') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                }
            }
        }
        
        stage('Frontend Unit Tests') {
            when { expression { params.RUN_TESTS } }
            steps {
                dir('frontend') {
                    sh 'npm run test:ci'
                }
            }
            post {
                always {
                    junit 'frontend/test-results/junit/*.xml'
                    archiveArtifacts artifacts: 'frontend/coverage/**/*', allowEmptyArchive: true
                }
            }
        }
        
        stage('Frontend Lint') {
            steps {
                dir('frontend') {
                    sh 'npm run lint'
                }
            }
        }
        
        stage('Frontend Build') {
            steps {
                dir('frontend') {
                    sh 'npm run build --prod'
                }
            }
        }
        
        // Backend
        stage('Backend Dependencies') {
            steps {
                dir('backend') {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }
        
        stage('Backend Unit Tests') {
            when { expression { params.RUN_TESTS } }
            steps {
                dir('backend') {
                    sh 'mvn test -Dtest=**/JasmineTest.java'
                    junit '**/target/surefire-reports/*.xml'
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'backend/target/surefire-reports/*.xml', allowEmptyArchive: true
                    jacoco execPattern: 'backend/target/jacoco.exec'
                }
            }
        }
        
        stage('Backend Integration Tests') {
            when { expression { params.RUN_TESTS } }
            steps {
                dir('backend') {
                    sh 'mvn verify -Dtest=**/IntegrationTest.java'
                }
            }
        }
        
        stage('Backend Build') {
            steps {
                dir('backend') {
                    sh 'mvn package -DskipTests'
                }
            }
        }
        
        // Build Docker Images
        stage('Build Docker Images') {
            steps {
                script {
                    // Construire l'image Docker pour le frontend
                    docker.build("${env.DOCKER_REGISTRY}/buy-01-frontend:${env.BUILD_ID}", 'frontend')
                    
                    // Construire l'image Docker pour le backend
                    docker.build("${env.DOCKER_REGISTRY}/buy-01-backend:${env.BUILD_ID}", 'backend')
                }
            }
        }
        
        // Push Docker Images
        stage('Push Docker Images') {
            steps {
                script {
                    // Pousser l'image Docker pour le frontend
                    docker.withRegistry("https://${env.DOCKER_REGISTRY}", env.DOCKER_CREDENTIALS) {
                        docker.image("${env.DOCKER_REGISTRY}/buy-01-frontend:${env.BUILD_ID}").push()
                    }
                    
                    // Pousser l'image Docker pour le backend
                    docker.withRegistry("https://${env.DOCKER_REGISTRY}", env.DOCKER_CREDENTIALS) {
                        docker.image("${env.DOCKER_REGISTRY}/buy-01-backend:${env.BUILD_ID}").push()
                    }
                }
            }
        }
        
        // Deploy with Docker
        stage('Deploy with Docker') {
            steps {
                script {
                    echo "Deploying to ${params.ENVIRONMENT} environment..."
                    
                    // Déployer le frontend
                    sh """
                        docker stop buy-01-frontend || true
                        docker rm buy-01-frontend || true
                        docker run -d -p 80:80 --name buy-01-frontend ${env.DOCKER_REGISTRY}/buy-01-frontend:${env.BUILD_ID}
                    """
                    
                    // Déployer le backend
                    sh """
                        docker stop buy-01-backend || true
                        docker rm buy-01-backend || true
                        docker run -d -p 8080:8080 --name buy-01-backend ${env.DOCKER_REGISTRY}/buy-01-backend:${env.BUILD_ID}
                    """
                }
            }
            post {
                success {
                    echo 'Deployment completed successfully!'
                }
                failure {
                    echo 'Deployment failed!'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}