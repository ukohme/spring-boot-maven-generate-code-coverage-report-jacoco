pipeline {
    agent any
    tools {
        maven "maven3"
    }
    environment {
      SCANNER_HOME = tool 'sonar_scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Let's clone the source
                    git branch: 'main', credentialsId: 'github_credentials_token', url: 'https://github.com/jmstechhome25/spring-boot-maven-generate-code-coverage-report-jacoco.git'
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('SonarQube analysis') {
           steps {
                 withSonarQubeEnv(credentialsId: 'sonar_credentials', installationName: 'loal_sonar') {
                 sh '''$SCANNER_HOME/bin/sonar-scanner'''
                }
              }
            }

        stage('Quality Gate') {
            steps {
                script {
                    // Check the SonarQube quality gate status
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Quality Gate failed: ${qualityGate.status}"
                    }
                }
            }
        }
    }
    post {
        failure {
            script {
                currentBuild.result = 'FAILURE'
            }
        }

        always {
            step([$class: 'Mailer',
                notifyEveryUnstableBuild: true,
                recipients: "jmstechhome24@gmail.com gompanarayanarao2@gmail.com",
                sendToIndividuals: true])
        }
    }
}
