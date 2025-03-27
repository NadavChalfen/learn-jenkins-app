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
                   
                }
            }

            steps {
                sh '''
                npm install serve
                npx serve -s build -l 5000 &
                node_modules/.bin/serve -s build &
                sleep 10
                npx playwright test
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
