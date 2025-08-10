pipeline {
    agent any
     environment {
        // SSH credentials ID from Jenkins
        SSH_KEY_ID = 'liraan'
        // EC2 username + public IP/DNS
        EC2_HOST = 'ec2-user@184.73.127.226'
        // Target directory on EC2
        DEPLOY_PATH = '/var/www/html'
        folder = '/homne'
    }

    stages {
        stage('Cleanup') {
            steps {
                deleteDir()
            }
        }
        stage ('testing') {
            
            steps {
                 sh '''
                    rm -rf adam_repo/adam.pem
                    git clone https://github.com/aiyubalikhan92/adam_repo.git
                    cd adam_repo
                    ls -la
                    pwd
                    '''
            }
        }
        stage('Build') {
            steps {
                echo "🔨 Building website (if needed)..."
                sh '''
                # For a static HTML site, no real build is needed
                # If you had a React/Vue app:
                # npm install && npm run build
                echo "No build required for static site."
                '''
            }
        }
        //  stage('Deploy') {
        //     steps {
        //         echo "🚀 Deploying to EC2..."
        //         sshagent(credentials: ['liraan']) {
        //             sh """
        //             # Copy all files to EC2's web directory
        //             rsync -avz -e "ssh -o StrictHostKeyChecking=no" ./ ${EC2_HOST}:${DEPLOY_PATH}

        //             # Restart web server (Apache or Nginx)
        //             ssh -o StrictHostKeyChecking=no ${EC2_HOST} 'sudo yum install -y httpd && sudo systemctl start httpd && sudo systemctl enable httpd'
        //             """
        //         }
        //     }
        // }
       stage('Deploy to EC2') {
    steps {
        sh '''
        cd adam_repo
        
            pwd
            ls
            
             chmod 400 adam.pem
              # 1. Install Apache on EC2
            ssh -i adam.pem -o StrictHostKeyChecking=no ${EC2_HOST} "
                sudo yum install -y httpd &&
                sudo systemctl start httpd &&
                sudo systemctl enable httpd &&
                sudo systemctl restart httpd 
                #sudo systemctl status httpd 
            ssh -i adam.pem -o StrictHostKeyChecking=no ec2-user@184.73.127.226 "mkdir -p /home/ec2-user/adam"
                
            ssh -i adam.pem -o StrictHostKeyChecking=no ec2-user@184.73.127.226 "cd /home/ec2-user/adam/ && ls -la && pwd && rm -rf *"
            scp -i adam.pem -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/adam/adam_repo/index.html ec2-user@184.73.127.226:/home/ec2-user/adam/
            


            echo  "TEST"
            ssh -i adam.pem -o StrictHostKeyChecking=no ${EC2_HOST} "cd /home/ec2-user/adam/ && ls -la && pwd && cat index.html"
             echo  "TEST"
 

            # 3. Move it into /var/www/html with sudo
            ssh -i adam.pem -o StrictHostKeyChecking=no ${EC2_HOST} "
                sudo mv /home/ec2-user/adam/index.html /var/www/html/
            "
        '''
    }
}


    }
}
