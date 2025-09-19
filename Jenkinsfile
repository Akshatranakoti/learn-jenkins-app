pipeline {
    agent any

    stages {
        stage('Remote Build & Deploy') {
            steps {
                sshagent (credentials: ['remote-deploy-key']) {
                    sh '''
                    echo "Connecting to remote server..."

                    ssh -o StrictHostKeyChecking=no ubuntu@<REMOTE_SERVER_IP> "
                        set -e

                        # Create directory if it doesn't exist
                        mkdir -p ~/learn-jenkins

                        cd ~/learn-jenkins

                        # Clone repo if not already present, else pull latest
                        if [ ! -d learn-jenkins-app ]; then
                            git clone https://github.com/Akshatranakoti/learn-jenkins-app.git
                        else
                            cd learn-jenkins-app
                            git pull origin main
                            cd ..
                        fi

                        cd learn-jenkins-app

                        # Check Node & NPM versions
                        node --version
                        npm --version

                        # Install dependencies and build
                        npm ci
                        npm run build
                    "
                    '''
                }
            }
        }
    }
}
