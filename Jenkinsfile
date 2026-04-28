pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'sonarserver'
        PROJECT_KEY   = 'go-project'
        PROJECT_NAME  = 'go-project'
        SCANNER_HOME  = tool 'sonarqube8.0'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamasfaa/demo-jenkins',
                    credentialsId: 'jenkinsUser'
            }
        }

        stage('Setup') {
            steps {
                sh 'echo setup'
            }
        }

        stage('Build') {
            steps {
                sh 'echo build'
            }
        }

        stage('Test') {
            steps {
                sh 'echo test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                          -Dsonar.projectKey=${PROJECT_KEY} \
                          -Dsonar.projectName=${PROJECT_NAME} \
                          -Dsonar.sources=. \
                          -Dsonar.go.coverage.reportPaths=coverage.out
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 20, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline sukses'
        }
        failure {
            echo 'Pipeline gagal'
        }
    }
}
