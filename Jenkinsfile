pipeline {
    agent any

    stages {
   /*    stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
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
*/

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
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
            ls -la build  # Check if build files exist
            npm ci  # Install dependencies
            npx playwright install --with-deps  # Ensure Playwright dependencies
            npm install serve  # Install serve locally
            npx serve -s build -l 5000 &  # Serve build files
            sleep 10  # Wait for server to start
            npx playwright test --trace on  # Run Playwright tests with tracing enabled
        '''
    }
}


    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
