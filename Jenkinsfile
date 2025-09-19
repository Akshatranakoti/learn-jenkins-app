stage('Remote Build & Deploy') {
    steps {
        sshagent(['ubuntu']) {
            sh '''
            echo "Connecting to remote server and building in Docker..."
            ssh -o StrictHostKeyChecking=no ubuntu@98.130.11.116 '
                mkdir -p ~/learn-jenkins
                cd ~/learn-jenkins

                if [ ! -d learn-jenkins-app ]; then
                    git clone https://github.com/Akshatranakoti/learn-jenkins-app.git
                else
                    cd learn-jenkins-app
                    git pull origin main
                    cd ..
                fi

                cd learn-jenkins-app

                docker run --rm -v ~/learn-jenkins/learn-jenkins-app:/app -w /app node:18-alpine sh -c "
                    npm ci
                    npm run build
                "
            '
            '''
        }
    }
}
