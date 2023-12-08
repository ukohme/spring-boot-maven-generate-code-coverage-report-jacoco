pipeline {
    agent any
    tools {
        // Note: this should match with the tool name configured in your jenkins instance (JENKINS_URL/configureTools/)
        maven "maven3"
    }
    environment {

        SONARQUBE_URL = 'http://ec2-13-234-239-23.ap-south-1.compute.amazonaws.com/'
        SONARQUBE_TOKEN = credentials('sonar_credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Let's clone the source
                    git 'https://github.com/jmstechhome25/spring-boot-maven-generate-code-coverage-report-jacoco.git';
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Scan') {
            steps {
                script {
                    // Run SonarQube analysis
                    withSonarQubeEnv('SonarQube Server') {
                        sh "mvn sonar:sonar -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=${SONARQUBE_TOKEN}"
                    }
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
