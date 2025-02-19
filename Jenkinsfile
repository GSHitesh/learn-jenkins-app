pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = 'e3e0983e-71af-4795-9c45-7b6205c6b4e5'
        NETLIFY_AUTH_TOKEN = credentials.netlify_token
    }
    stages {
        /*

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
        */
        stage('Test') {
            parallel {
                stage('Unit Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                    #test -f build/index.html
                    npm test
                '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }
        stage("Deploy"){
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            steps{
                sh '''
                    echo "Deploying the application"
                    npm install netlify-cli
                    echo "Deploying the application"
                    echo "Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify deploy --prod --dir=build

                '''
                }
            }
        }
    }
}
