pipeline {
    agent any
    
    environment{
       NETLIFY_SITE_ID = '90a56215-564d-4ed6-8b37-bdfa329a457f' 
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

        stage('Run Tests') {
            parallel {
                stage('Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        echo 'Test stage'
                        sh '''
                        test -f build/index.html
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
                        echo 'E2E stage'
                        sh '''
                        npm install serve
                        node_modules/.bin/serve -s build &
                        sleep 10
                        npx playwright test
                        '''
                    }
                    post {
                        always {
                            publishHTML([
                                allowMissing: false,
                                alwaysLinkToLastBuild: false,
                                icon: '',
                                keepAll: false,
                                reportDir: 'playwright-report',
                                reportFiles: 'index.html',
                                reportName: 'Playwright Local',
                                reportTitles: '',
                                useWrapperFileDirectly: true
                            ])
                        }
                    }
                }
            }
        }
       
        stage('Deploy Staging') {
             environment{
                        CI_ENVIRONMENT_URL= 'Site url to be set'
                       }
                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                    }
                }
                steps {
                   sh '''
                    npm install netlify-cli@20.1.1 node-jq
                    node_modules/.bin/netlify --version
                    echo "Deploying to production .SITE ID : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                    CI_ENVIRONMENT_URL=$(node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json)
                    npx playwright test --reporter=html
                    '''
                }
                post {
                    always {
                        publishHTML([
                            allowMissing: false,
                            alwaysLinkToLastBuild: false,
                            icon: '',
                            keepAll: false,
                            reportDir: 'playwright-report',
                            reportFiles: 'index.html',
                            reportName: 'Staging E2E',
                            reportTitles: '',
                            useWrapperFileDirectly: true
                        ])
                    }
                }
            }        
        stage('Approval'){
         steps{
            timeout(time: 1, unit: 'MINUTES') {
                
                input 'Ready to deploy?'
             }
            
         }
        }
  
        stage('Deploy Prod') {
             environment{
                        CI_ENVIRONMENT_URL='https://unrivaled-maamoul-921fa9.netlify.app'
                       }
                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                    }
                }
                steps {
                   sh '''
                        echo "chages made"
                        npm install netlify-cli@20.1.1
                        node_modules/.bin/netlify --version
                        echo "Deploying to production .SITE ID : $NETLIFY_SITE_ID"
                        node_modules/.bin/netlify status
                        node_modules/.bin/netlify deploy --dir=build --prod
                        npx playwright test --reporter=html
                    '''
                }
                post {
                    always {
                        publishHTML([
                            allowMissing: false,
                            alwaysLinkToLastBuild: false,
                            icon: '',
                            keepAll: false,
                            reportDir: 'playwright-report',
                            reportFiles: 'index.html',
                            reportName: 'Playwright E2E',
                            reportTitles: '',
                            useWrapperFileDirectly: true
                        ])
                    }
                }
            }
    }
}
