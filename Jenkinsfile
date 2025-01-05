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
                    url: "https://github.com/AjinkyaBapat/Netflix-clone.git",
                    credentialsId: "github-cred",
                    branch: "main"
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
                    additionalArguments: '--scan ./',
                    odcInstallation: 'owasp-check',
                    nvdCredentialsId: 'nvd-api-key',
                    publisherFilePattern: '**/dependency-check-report.xml'
                )
            }
        }

        // stage('Trivy FS Scan') {
        //     steps {
        //         sh 'trivy fs --format json -o trivy-fs-report.json .'
        //         recordIssues enabledForFailure: true,
        //                      sourceCodeRetention: 'LAST_BUILD',
        //                      tools: [trivy(pattern: '**/trivy-fs-report.json')]
        //         sh 'trivy scan2html generate --scan2html-flags --output Trivy-FS-ScanReport.html --from trivy-fs-report.json'
        //     }
        // }

        // stage('Docker Build and Push') {
        //     steps {
        //         script {
        //             withDockerRegistry(toolName: 'docker-tool', credentialsId: 'docker-cred') {
        //                 sh 'docker build --build-arg TMDB_V3_API_KEY=${TMDb_V3_API_KEY} -t netflix-clone .'
        //                 sh 'docker tag netflix-clone iamajinkya/netflix-clone:${BUILD_NUMBER}'

        //             }
        //         }
        //     }
        // }
    }
}

