pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/shreyasnm9/Hello-World-Springboot-App.git'
        BASE_PATH = 'Hello-World-Springboot-App/'
        EC2_INSTANCE = 'ec2-user@<Your-EC2-Instance-IP>'
        SSH_CREDENTIALS_ID = 'aws-ssh-credentials'  // Jenkins SSH credentials
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_LOGIN = credentials('sonaqube') // SonarQube token in Jenkins Credentials
    }

    stages {
        stage('Build') {
            steps {
                echo 'Cloning the repository...'
                dir(${BASE_PATH}){
                    sh 'rm -rf ${BASE_PATH}'
                    sh 'git clone ${REPO_URL}'
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Running Unit Tests...'
                dir(${BASE_PATH}){
                    sh "mvn test"
                }
            }
        }

        stage('SonarQube Code Quality Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        sh """
                            cd ${BASE_PATH}
                            sonar-scanner -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_LOGIN}
                        """.stripIndent().trim()
                    }
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                script {
                    dir(${BASE_PATH}){
                        echo 'Creating JAR file'
                        sh "mvn clean package"
                    }
                }
            }
        }

        stage('Release') {
            steps {
                script {
                    echo 'Releasing to production...'
                    // Add specific release steps like tagging a Git commit or notifying the team.
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
