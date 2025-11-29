pipeline {
    agent any

    environment {
        // Define any environment variables here
        NETLIFY_SITE_ID = '3e5668fa-76b2-4b8e-b8a6-497ba7e88ffa'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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

        stage('Test'){
            
            parallel{
                
                stage('unitTest') {
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
                            junit 'test-results/junit.xml'
                        }
                    }
                }

                // stage('E2E') {
                //     agent {
                //         docker {
                //             image 'mcr.microsoft.com/playwright:v1.57.0-noble'
                //             reuseNode true
                //         }
                //     }

                //     steps {
                //         sh '''
                //             npm install -e serve
                //             serve -s build &
                //             npx playwright test
                //         '''
                //     }
                // }
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
                            echo "Deploying to Netlify site ID: $NETLIFY_SITE_ID"
                            node_modules/.bin/netlify status --site $NETLIFY_SITE_ID --auth $NETLIFY_AUTH_TOKEN
                        '''
                    }
                }
        
    }
}