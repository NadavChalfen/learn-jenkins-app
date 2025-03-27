pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-v /var/jenkins_home/workspace:/var/jenkins_home/workspace'
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-v /var/jenkins_home/workspace:/var/jenkins_home/workspace'
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.51.1-noble'
                    args '-v /var/jenkins_home/workspace:/var/jenkins_home/workspace'
                }
            }
            steps {
                sh '''
                    npm ci  # Ensure dependencies are installed
                    npm install serve  # Install serve locally
                    npx serve -s build &  # Serve the build directory
                    sleep 10  # Allow server time to start
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            script {
                if (fileExists('test-results/junit.xml')) {
                    junit 'test-results/junit.xml'
                } else {
                    echo "JUnit test results not found, skipping report."
                }
            }
        }
    }
}
