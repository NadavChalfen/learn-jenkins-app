pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID = 'b001fb9c-9dee-4801-ba6f-230b697061a7'
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
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

        stage('Tests') {
            parallel {
                stage('Unit Test') {
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
                            npm install -g serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                }
            }
        }

        // Post section for the entire 'Tests' stage
        stage('Post-Test') {
            steps {
                junit 'jest-results/junit.xml'
                publishHTML([
                    allowMissing: false, 
                    alwaysLinkToLastBuild: false, 
                    icon: '', 
                    keepAll: false, 
                    reportDir: 'playwright-report', 
                    reportFiles: 'index.html', 
                    reportName: 'Playwright HTML Report', 
                    reportTitles: '', 
                    useWrapperFileDirectly: true
                ])
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    npm install netlify-cli
                     node_modules/.bin/netlify --version
                '''
            }
        }
    }
}