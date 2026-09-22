pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Verify SonarQube Token') {
    steps {
        script {
            withCredentials([
                string(credentialsId: 'jenkins-token', variable: 'SONAR_TOKEN')
            ]) {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        STATUS=$(curl -s -o /tmp/sonar-auth.json -w "%{http_code}" \
                          -u "$SONAR_TOKEN:" \
                          "$SONAR_HOST_URL/api/authentication/validate")

                        echo "SonarQube authentication HTTP status: $STATUS"

                        cat /tmp/sonar-auth.json
                    '''
                }
            }
        }
    }
}
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'

                    withCredentials([
                        string(credentialsId: 'jenkins-token', variable: 'SONAR_TOKEN')
                    ]) {
                        withSonarQubeEnv('sonarqube') {
                            sh "${scannerHome}/bin/sonar-scanner -Dsonar.token=\"${SONAR_TOKEN}\""
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
