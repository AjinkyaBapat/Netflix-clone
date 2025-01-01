pipeline {
    agent any

    tools {
        jdk 'jdk18'
        nodejs 'node22'
    }

    environment {
        SCANNNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AjinkyaBapat/Netflix-clone.git'
            }
        }

        stage('Sonar analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        ${SCANNNER_HOME}/bin/sonar-scanner -Dsonar.projectName=Netflix \
                        -Dsonar.projectKey=Netflix
                    '''
                }
            }
        }

        stage('Sonar Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp-check', nvdCredentialsId: 'nvd-api-key'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
            }
    }
}

