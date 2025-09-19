pipeline {
    agent any

    stages {
        stage('Remote Build & Deploy') {
            steps {
                sshagent (credentials: ['remote-deploy-key']) {
                    sh '''
                    echo "Connecting to remote server and building in Docker..."

                    ssh -o StrictHostKeyChecking=no ubuntu@98.130.11.116 "
                        set -e

                        # Create base directory if it doesn't exist
                        mkdir -p ~/learn-jenkins
                        cd ~/learn-jenkins

                        # Clone repo if not present, else pull latest
                        if [ ! -d learn-jenkins-app ]; then
                            git clone https://github.com/Akshatranakoti/learn-jenkins-app.git
                        else
                            cd learn-jenkins-app
                            git pull origin main
                            cd ..
                        fi

                        cd learn-jenkins-app

                        # Build using Node Docker container
                        docker run --rm -v \$(pwd):/app -w /app node:18-alpine sh -c \"
                            npm ci &&
                            npm run build
                        \"
                    "
                    '''
                }
            }
        }
    }
}
