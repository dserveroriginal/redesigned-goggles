pipeline {
    agent any

    environment {
        REPO_APP = "https://github.com/mtararujs/python-greetings"
        REPO_TESTS = "https://github.com/mtararujs/course-js-api-framework"
    }

    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    build()
                }
            }
        }

        stage('deploy-to-dev') {
            steps {
                script {
                    deploy('dev', 7001)
                }
            }
        }

        stage('tests-on-dev') {
            steps {
                script {
                    test('dev')
                }
            }
        }

        stage('deploy-to-staging') {
            steps {
                script {
                    deploy('stg', 7002)
                }
            }
        }

        stage('tests-on-staging') {
            steps {
                script {
                    test('stg')
                }
            }
        }

        stage('deploy-to-preprod') {
            steps {
                script {
                    deploy('preprod', 7003)
                }
            }
        }

        stage('tests-on-preprod') {
            steps {
                script {
                    test('preprod')
                }
            }
        }

        stage('deploy-to-prod') {
            steps {
                script {
                    deploy('prod', 7004)
                }
            }
        }

        stage('tests-on-prod') {
            steps {
                script {
                    test('prod')
                }
            }
        }
    }
}

def build() {
    echo "Cloning application repository..."
    bat "rmdir /s /q python-greetings & git clone %REPO_APP%"
    
    echo "Verifying files in the repository..."
    bat "dir python-greetings"

    echo "Installing required Python dependencies..."
    bat "pip install -r python-greetings/requirements.txt"
    
    bat "npm install pm2"
}

def deploy(envName, port) {
    echo "Deploying application to ${envName} environment on port ${port}..."
    
    echo "Cloning application repository..."
    bat "rmdir /s /q python-greetings & git clone %REPO_APP%"

    echo "Stopping any existing service..."
    bat "node_modules\\.bin\\pm2 delete \"greetings-app-${envName}\" || exit 0"

    echo "Starting application..."
    bat "node_modules\\.bin\\pm2 start -n \"greetings-app-${envName}\" python-greetings/app.py -- ${port} -- --port ${port}"
    // bat "timeout 10"
}

def test(envName) {
    echo "Running tests on ${envName} environment..."

    bat "node_modules\\.bin\\pm2 list"

    echo "Cloning test framework repository..."
    bat "rmdir /s /q course-js-api-framework & git clone %REPO_TESTS%"

    echo "Installing required Node.js dependencies..."
    bat "cd course-js-api-framework && npm install"

    echo "Executing API tests..."
    bat "cd course-js-api-framework && npm run greetings greetings_${envName}"
}
