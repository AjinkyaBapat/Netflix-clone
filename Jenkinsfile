pipeline {
    agent any

    tools {
        jdk 'jdk18'
        nodejs 'node22'
    }

    environment {
        SCANNNER_HOME = tool 'sonar-scanner'
        TMDb_V3_API_KEY = credentials('tmdb-api-key')
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                gitCheckout(
                    url: 'https://github.com/AjinkyaBapat/Netflix-clone.git',
                    credentialsId: 'github-cred',
                    branch: 'main'
                    )
            }
        }

        stage('Sonar analysis') {
            steps {
                sonarAnalysis(
                    sonarServer: 'sonar-server',
                    projectName: 'Netflix',
                    projectKey: 'Netflix',
                    abortPipeline: false,
                    credentialsId: 'Sonar-token',
                    scannerHome: SCANNNER_HOME
                )
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                owaspdependencyCheck(
                    additionalArguments: '--scan ./ --disableYarnAudit',
                    odcInstallation: 'owasp-check',
                    nvdCredentialsId: 'nvd-api-key',
                    publisherFilePattern: '**/dependency-check-report.xml'
                )
            }
        }

        stage('Trivy FS Scan') {
            steps {
                trivyScan()
            }
        }

        stage('Docker Build and Push') {
            steps {
                dockerBuildandPush(
                    toolName: 'docker-tool',
                    credentialsId: 'docker-cred',
                    buildArg: 'TMDB_V3_API_KEY=$TMDb_V3_API_KEY',
                    registryName: 'iamajinkya',
                    imageName: 'netflix-clone'
                )
                }
            }
        }
}

