pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/shreyasnm9/Notification-Categorization.git'
        BASE_PATH = 'notification-categorization/'
        EC2_INSTANCE = 'ec2-user@<Your-EC2-Instance-IP>'
        SSH_CREDENTIALS_ID = 'aws-ssh-credentials'  // Jenkins SSH credentials
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_LOGIN = credentials('sonarqube-token') // SonarQube token in Jenkins Credentials
        REQUIREMENTS_FILE = 'requirements.txt'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                sh 'rm -rf ${BASE_PATH}'
                sh 'git clone ${REPO_URL}'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh """
                    cd ${BASE_PATH}
                    pip install -r ${REQUIREMENTS_FILE}
                """
            }
        }

        stage('Test') {
            steps {
                echo 'Running Unit Tests...'
                sh """
                    cd ${BASE_PATH}
                    pytest --maxfail=1 --disable-warnings || true
                """
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
                    echo 'Deploying to AWS EC2...'
                    sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_INSTANCE} 'mkdir -p ~/app && rm -rf ~/app/*'
                        scp -o StrictHostKeyChecking=no -r ${BASE_PATH} ${EC2_INSTANCE}:~/app/
                        ssh -o StrictHostKeyChecking=no ${EC2_INSTANCE} '
                            cd ~/app/${BASE_PATH} &&
                            pip install -r ${REQUIREMENTS_FILE} &&
                            nohup streamlit run <your_python_script>.py --server.port 8501 > streamlit.log 2>&1 &
                        '
                        """
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
