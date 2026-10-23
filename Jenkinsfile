pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                sh 'mvn -B clean verify'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh '''
                        mvn -B org.sonarsource.scanner.maven:sonar-maven-plugin:3.10.0.2594:sonar \
                          -Dsonar.projectKey=hello-world \
                          -Dsonar.projectName=hello-world
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo 'Build and SonarQube Quality Gate passed.'
        }
        failure {
            echo 'Build failed. Review the Jenkins console output.'
        }
    }
}
