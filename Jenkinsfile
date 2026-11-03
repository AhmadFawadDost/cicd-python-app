pipeline {
    agent any

    environment {
        WEBEX_BOT_TOKEN = credentials('webex-bot-token')
        WEBEX_ROOM_ID = credentials('webex-room-id')
    }

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

    post {
        success {
            sh '''
                curl --fail --silent --show-error \
                -X POST "https://webexapis.com/v1/messages" \
                -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                -H "Content-Type: application/json" \
                -d "{\"roomId\":\"$WEBEX_ROOM_ID\",\"text\":\"Jenkins build SUCCESS: $JOB_NAME #$BUILD_NUMBER\"}"
            '''
        }

        failure {
            sh '''
                curl --fail --silent --show-error \
                -X POST "https://webexapis.com/v1/messages" \
                -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                -H "Content-Type: application/json" \
                -d "{\"roomId\":\"$WEBEX_ROOM_ID\",\"text\":\"Jenkins build FAILED: $JOB_NAME #$BUILD_NUMBER\"}"
            '''
        }
    }
}