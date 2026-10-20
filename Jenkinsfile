pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                sh 'python3 -m pip install --user -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'python3 -m unittest discover -s .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                    mkdir -p "${WORKSPACE}/python-app-deploy"
                    cp "${WORKSPACE}/app.py" "${WORKSPACE}/python-app-deploy/"
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Starting application process...'
                sh '''
                    nohup python3 "${WORKSPACE}/python-app-deploy/app.py" \
                        > "${WORKSPACE}/python-app-deploy/app.log" 2>&1 &
                    echo $! > "${WORKSPACE}/python-app-deploy/app.pid"
                    sleep 3
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing the deployed application...'
                sh '''
                    cd "${WORKSPACE}"
                    python3 -m unittest discover -s .
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
