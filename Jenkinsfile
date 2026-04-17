pipeline{
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        DOCKER_IMAGE : "tawfeeq421/game"
        DOCKER_TAG : "${BUILD_NUMBER}"
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
        
    }
}