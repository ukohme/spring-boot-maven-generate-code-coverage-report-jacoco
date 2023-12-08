pipeline {
    agent any
    tools {
        // Note: this should match with the tool name configured in your jenkins instance (JENKINS_URL/configureTools/)
        maven "maven3"
    }
    environment {

        SONARQUBE_URL = 'http://ec2-13-234-239-23.ap-south-1.compute.amazonaws.com'
        SONARQUBE_TOKEN = credentials('sonar_credentials')
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
    environment {
      SCANNER_HOME = tool 'sonar_scanner'
    }
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

}
