pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'python3 calculator.py'
            }
        }

        stage('Test') {
            steps {
                sh 'python3 -m unittest -v'
            }
        }
    }
}