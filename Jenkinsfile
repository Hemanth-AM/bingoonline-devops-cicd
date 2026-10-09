pipeline {
    agent any

    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Clean the workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Update-Baseline') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Shikhar82/onlinegame.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=BingoOnlineProject \
                        -Dsonar.sources=.
                    '''
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                script {
                    waitForQualityGate(
                        abortPipeline: false,
                        credentialsId: 'sonar-token'
                    )
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('OWASP FS Scan') {
            steps {
                dependencyCheck(
                    additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit',
                    odcInstallation: 'DP',
                    nvdCredentialsId: 'nvd-api-key'
                )

                dependencyCheckPublisher(
                    pattern: '**/dependency-check-report.xml'
                )
            }
        }

        stage('Trivy FS Scanning') {
            steps {
                sh '''
                    trivy fs . \
                    --db-repository ghcr.io/aquasecurity/trivy-db \
                    --timeout 15m \
                    --severity HIGH,CRITICAL \
                    --format table \
                    > trivyscanreport.txt
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                    -t bingoonlineproject:latest \
                    .
                '''
            }
        }

        stage('Trivy Docker Image Scan') {
            steps {
                sh '''
                    trivy image \
                    --db-repository ghcr.io/aquasecurity/trivy-db \
                    --timeout 15m \
                    --severity HIGH,CRITICAL \
                    --format table \
                    bingoonlineproject:latest
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-jenkins',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            --username "$DOCKER_USERNAME" \
                            --password-stdin

                        docker tag \
                            bingoonlineproject:latest \
                            hemantham666/bingoonlineproject:latest

                        docker push \
                            hemantham666/bingoonlineproject:latest

                        docker logout
                    '''
                }
            }
        }
        
        
         stage('Deploy the Docker Image/Nodejs App to the Env as container') {
            steps {
                sh 'docker run -d --name bingoonline -p 3000:3000 bingoonlineproject:latest'
            }
        }
    }
}
