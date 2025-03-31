def deployService(envName, port) {
    echo "Deploying to ${envName} environment..."
    git branch: "main", url: "https://github.com/mtararujs/python-greetings"
    sh "pm2 delete greetings-app-${envName} || true"
    sh """
        . venv/bin/activate
        pm2 start app.py --name greetings-app-${envName} -- --port ${port}
    """
}

def testService(envName) {
    echo "Testing on ${envName} environment..."
    git branch: "main", url: "https://github.com/mtararujs/course-js-api-framework"
    sh "npm install"
    sh "npm run greetings greetings_${envName}"
}

pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                echo "Cloning python-greetings repository..."
                git branch: "main", url: "https://github.com/mtararujs/python-greetings"
                echo "Listing repository contents..."
                sh 'ls -la'
                echo "Installing pip dependencies..."
                script {
                    sh '''
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('deploy-to-dev') {
            steps {
                script {
                    deployService("dev", "7001")
                }
            }
        }

        stage('tests-on-dev') {
            steps {
                script {
                    testService("dev")
                }
            }
        }

        stage('deploy-to-staging') {
            steps {
                script {
                    deployService("staging", "7002")
                }
            }
        }

        stage('tests-on-staging') {
            steps {
                script {
                    testService("stg")
                }
            }
        }

        stage('deploy-to-preprod') {
            steps {
                script {
                    deployService("preprod", "7003")
                }
            }
        }

        stage('tests-on-preprod') {
            steps {
                script {
                    testService("preprod")
                }
            }
        }

        stage('deploy-to-prod') {
            steps {
                script {
                    deployService("prod", "7004")
                }
            }
        }

        stage('tests-on-prod') {
            steps {
                script {
                    testService("prod")
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
