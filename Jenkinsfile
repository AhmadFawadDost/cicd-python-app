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
                python3 - <<'PY'
import json
import os

payload = {
    "roomId": os.environ["WEBEX_ROOM_ID"],
    "text": "Jenkins build SUCCESS: {} #{}".format(
        os.environ["JOB_NAME"],
        os.environ["BUILD_NUMBER"]
    )
}

with open("/tmp/webex-message.json", "w") as f:
    json.dump(payload, f)
PY

                curl --fail --silent --show-error \
                  -X POST "https://webexapis.com/v1/messages" \
                  -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                  -H "Content-Type: application/json" \
                  --data @/tmp/webex-message.json

                rm -f /tmp/webex-message.json
            '''
        }

        failure {
            sh '''
                python3 - <<'PY'
import json
import os

payload = {
    "roomId": os.environ["WEBEX_ROOM_ID"],
    "text": "Jenkins build FAILED: {} #{}".format(
        os.environ["JOB_NAME"],
        os.environ["BUILD_NUMBER"]
    )
}

with open("/tmp/webex-message.json", "w") as f:
    json.dump(payload, f)
PY

                curl --fail --silent --show-error \
                  -X POST "https://webexapis.com/v1/messages" \
                  -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                  -H "Content-Type: application/json" \
                  --data @/tmp/webex-message.json

                rm -f /tmp/webex-message.json
            '''
        }
    }
}