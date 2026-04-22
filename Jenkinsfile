pipeline {
    agent any

    tools {
        jdk 'JDK17'
        nodejs 'NODE18'
    }

    environment {
        DOCKER_IMAGE = "tawfeeq421/game"
        DOCKER_TAG = "${BUILD_NUMBER}"
        AWS_REGION = 'us-west-2'
        CLUSTER_NAME = 'my-cluster'
        NAMESPACE = 'game'
        IMAGE = "${DOCKER_IMAGE}:${DOCKER_TAG}"
    }

    stages {

        stage('Clean Workspace') {
            steps { cleanWs() }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/tawfeeq421/react-game.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('SonarQube Scan') {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
                withSonarQubeEnv('sonarserver'){
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=react-game \
                    -Dsonar.projectName=react-game \
                    -Dsonar.sources=. \
                    '''
                }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh '''
                trivy fs . \
                --severity HIGH,CRITICAL \
                --format table \
                -o trivy-report.txt || true
                '''
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
                        def app = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                        app.push()
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh """
                trivy image \
                --severity HIGH,CRITICAL \
                --format table \
                -o trivy-image-report.txt \
                ${DOCKER_IMAGE}:${DOCKER_TAG} || true
                """
            }
        }

        stage('Deploy to EKS') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    set -e

                    aws eks --region $AWS_REGION update-kubeconfig --name $CLUSTER_NAME

                    kubectl apply -f k8s/namespace.yml

                    kubectl apply -f k8s/game-app-deployment.yml -n $NAMESPACE
                    kubectl apply -f k8s/game-app-service.yml -n $NAMESPACE
                    kubectl apply -f k8s/ingress.yml -n $NAMESPACE

                    kubectl set image deployment/game-app-deployment game=$IMAGE -n $NAMESPACE

                    kubectl rollout status deployment/game-app-deployment -n $NAMESPACE
                    '''
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'trivy-report.txt, trivy-image-report.txt', fingerprint: true
        }

        success {
            slackSend(
                channel: "#devopscicd",
                color: "good",
                message: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}\nImage: ${DOCKER_IMAGE}:${DOCKER_TAG}"
            )
        }

        failure {
            slackSend(
                channel: "#devopscicd",
                color: "danger",
                message: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}"
            )
        }
    }
}