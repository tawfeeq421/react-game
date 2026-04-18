pipeline{
    agent any
    tools {
        jdk 'JDK17'
        nodejs 'NODE18'
    }
    environment {
        DOCKER_IMAGE : "tawfeeq421/game"
        DOCKER_TAG : "${BUILD_NUMBER}"
        AWS_REGION = 'us-west-2'
        CLUSTER_NAME = 'my-cluster'
        NAMESPACE ='game'
        IMAGE = "${DOCKER_IMAGE}:${DOCKER_TAG}"
    }
    stages{
        stage('Clean Workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Git Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/tawfeeq421/react-game.git'
            }
        }
        stage('Install Dependency'){
            steps{
                sh 'npm install'
            }
        }
        stage('Jest Test'){
            steps{
                sh 'npm test -- --wtachAll --coverage'
            }
        }
        stage('SonarQube Analysis'){
            environment{
                scannerHome = tool 'sonar'
            }
            steps{
                withSonarQubeEnv('sonnarserver'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                timeout(time: 1, unit: 'HOURS'){
                   waitForQualityGate abordPipeline: true
                }
            }
        }
        stage('Trivy FS Scan'){
            steps{
                sh '''
                trivy fs . \
                --severity HIGH,CRITICAL \
                --format table \
                -o trivy-report.txt
                '''
            }
        }
        stage('Docker Build & Push'){
            steps{
                script{
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred'){
                        def app = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                        app.push()
                    }
                }
            }
        }
        stage('Trivy Image Scan'){
            steps{
                sh """
                trivy image \
                --severity HIGH,CRITICAL \
                --format table \
                -o trivy-image-report.txt \
                ${DOCKER_IMAGE}:${DOCKER_TAG} || true
                """
            }
        }
        
    }
}