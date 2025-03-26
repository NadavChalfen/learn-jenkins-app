pipeline {
    agent any

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

       

            stage('E2E') {
                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.51.1-noble'
                        reuseNode true
                    }
                }
                steps {
                    unstash 'build-artifacts'
                    sh '''
                        npm install serve
                        nohup node_modules/.bin/serve -s build -l 5000 > serve.log 2>&1 &
                        sleep 5
                        tail serve.log
                        npx playwright install --with-deps
                        npx playwright test --reporter=html
                    '''
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
               npm install netlify-cli -g
               netlify --version

                '''
            
            }
        }



            }
        }


      
    

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }

