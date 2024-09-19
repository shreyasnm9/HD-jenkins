pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/shreyasnm9/Hello-World-Springboot-App.git'
        BASE_PATH = 'Hello-World-Springboot-App/'
        EC2_INSTANCE = 'ec2-user@3.7.46.89'
        SSH_CREDENTIALS_ID = 'aws-ssh'  // Jenkins SSH credentials
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_LOGIN = credentials('sonarqube') // SonarQube token in Jenkins Credentials
    }

    stages {
        stage('Build') {
            steps {
                echo 'Cloning the repository...'
                dir('Hello-World-Springboot-App/Hello-World-Springboot-App'){
                    sh 'rm -rf ${BASE_PATH}'
                    sh 'git clone ${REPO_URL}'
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Running Unit Tests...'
                dir('Hello-World-Springboot-App/Hello-World-Springboot-App'){
                    sh "mvn test"
                }
            }
        }

        /*stage('SonarQube Code Quality Analysis') {
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
        }*/

        stage('Deploy to AWS EC2') {
            steps {
                script {
                    dir('Hello-World-Springboot-App/Hello-World-Springboot-App'){
                        echo 'Creating JAR file'
                        sh "mvn clean package"
                        echo 'adding secure copy'
                        echo "Copying the JAR file to the EC2 instance..."
                        scp -o StrictHostKeyChecking=no $WORKSPACE/target/$JAR_FILE $EC2_USER@$EC2_IP:$EC2_DIR
                        echo "Starting the application on EC2..."
                        ssh -o StrictHostKeyChecking=no $EC2_USER@$EC2_IP "java -jar $EC2_DIR/$JAR_FILE"
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
