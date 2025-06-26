pipeline {
    agent { label 'rocky' }

    stages {
        stage('Install Git') {
            step {
                echo "Install git to Rocky Linux"
                sudo dnf install -y git
            }
        }


        stage('Install Apache') {
            steps {
                sh '''
                    echo "Updating system packages"
                    sudo dnf update -y

                    echo "Installing Apache (httpd)"
                    sudo dnf install -y httpd

                    echo "Enabling Apache to start on boot"
                    sudo systemctl enable httpd

                    echo "Starting Apache"
                    sudo systemctl start httpd

                    echo "Checking Apache status"
                    sudo systemctl status httpd || true

                    echo "Installation complete"
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "Checking if Apache is listening on port 80"
                    sudo ss -tulpn | grep :80 || echo "Apache is not listening on port 80"

                    echo "Checking if index.html exists"
                    ls -l /var/www/html || echo "Directory /var/www/html does not exist"
                '''
            }
        }

    }
}
