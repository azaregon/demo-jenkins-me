pipeline {
    agent {
        docker {
            image 'golang:1.23'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        SONARQUBE_ENV = 'sonarserver'
        PROJECT_KEY = 'go-project'
        PROJECT_NAME = 'go-project'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamasfaa/demo-jenkins',
                    credentialsId: 'jenkinsUser'
            }
        }

        stage('Setup Go') {
            steps {
                sh 'go version'
                sh 'go mod tidy'
            }
        }

        stage('Build') {
            steps {
                sh 'go build -v ./...'
            }
        }

        stage('Test') {
            steps {
                sh 'go test ./... -v -coverprofile=coverage.out'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh """
                    sonar-scanner \
                      -Dsonar.projectKey=${PROJECT_KEY} \
                      -Dsonar.projectName=${PROJECT_NAME} \
                      -Dsonar.sources=. \
                      -Dsonar.language=go \
                      -Dsonar.go.coverage.reportPaths=coverage.out
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
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