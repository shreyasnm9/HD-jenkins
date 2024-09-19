pipeline {
    agent any

    environment {
        DIRECTORY_PATH = "/path/to/code"
        TESTING_ENVIRONMENT = "TestEnv"
        PRODUCTION_ENVIRONMENT = "YourName" // Replace 'YourName' with your actual name
    }

    stages {
        stage('Build') {
            steps {
                echo "Fetch the source code from the directory path specified by DIRECTORY_PATH environment variable"
                echo "Compile the code and generate any necessary artifacts"
            }
        }
        stage('Test') {
            steps {
                echo "Unit tests"
                echo "Integration tests"
            }
        }
        stage('Code Quality Check') {
            steps {
                echo "Check the quality of the code"
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploy the application to a testing environment: ${TESTING_ENVIRONMENT}"
            }
        }
        stage('Approval') {
            steps {
                // echo "Waiting for approval..."
                sleep 10 // Sleep for 10 seconds to simulate manual approval
            }
        }
        stage('Deploy to Production') {
            steps {
                echo "Deploy the code to the production environment: ${PRODUCTION_ENVIRONMENT}"
            }
        }
    }
	post{
        success {
            emailext body: '[SUCCESS] Jenkins Week 6 Activity : Build Success',
                subject: '🟢 Jenkins Build | Test Subject',
                attachLog: true,
                to: 'shreyas9201@gmail.com'
        }
        failure {
            emailext body: '[FAILURE] Jenkins Week 6 Activity : Build Success',
                subject: '🔴 Jenkins Build | Test Subject',
                attachLog: true,
                to: 'shreyas9201@gmail.com'
        }
    }
}
//Experiment to prove the workikng of jenkins and github connection
