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
                echo "📦 Installing dependencies and building project..."
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['remote-deploy-key']) {
                    sh '''
                    echo "🚀 Deploying build to remote server..."

                    # Copy build artifacts to remote server (adjust path as needed)
                    scp -o StrictHostKeyChecking=no -r build/* ubuntu@98.130.11.116:/var/www/html/

                }
            }
        }
    }
}
