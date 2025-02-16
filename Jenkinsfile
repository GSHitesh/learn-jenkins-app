pipeline {
    agent any

    stages {
        // stage('Build') {
        //     agent{
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //         }
        //     steps {
        //         sh '''
        //         ls -la
        //         node --version
        //         npm --version
        //         npm ci
        //         npm run build
        //         ls -la
        //         '''
                
        //     }
        // }
        // stage('Test'){
        //     agent{
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //         }
        //     steps{
        //     sh '''
        //     echo 'Test stage'
        //     test -f build/index.html
        //     npm test
        //     '''
        //     }
        // }
        stage('E2E'){
            agent{
                docker {
                    image 'docker pull mcr.microsoft.com/playwright:v1.50.1-noble'
                    reuseNode true
                }
            }
            steps{
                sh '''
                echo 'E2E stage'
                npm install serve
                node_modules/.bin/serve -s build &
                sleep 10
                npx playwright test --reporter=html

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
