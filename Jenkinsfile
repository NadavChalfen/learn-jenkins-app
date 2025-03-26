pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker 'node:18-alpine'
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

        stage('Test'){
            steps{
                echo 'Test stage'
            }

        }


    }
}
