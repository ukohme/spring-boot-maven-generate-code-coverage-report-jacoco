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
                    git branch: 'main', credentialsId: 'gitadmin', url: 'https://github.com/ukohme/spring-boot-maven-generate-code-coverage-report-jacoco.git'
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
                 withSonarQubeEnv(credentialsId: 'sonar_credentials', installationName: 'local_sonar') {
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
  }
