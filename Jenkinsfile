pipeline {
    agent { label 'ubuntu' } 

    stages {

        stage('Install Apache') {
            steps {
                sh '''
                echo "Updating system packages"
                sudo apt update -y

                echo "Installing Apache (apache2)"
                sudo apt install -y apache2

                echo "Checking for apache2.conf presence..."
                if [ ! -f "/etc/apache2/apache2.conf" ]; then
                    echo "ERROR: /etc/apache2/apache2.conf not found!"
                    echo "Attempting to reinstall apache2 to fix missing files..."
                    sudo apt install --reinstall -y apache2 || { echo "Reinstallation failed, exiting."; exit 1; }
                    
                    if [ ! -f "/etc/apache2/apache2.conf" ]; then
                        echo "ERROR: /etc/apache2/apache2.conf still not found after reinstall! Manual intervention required."
                        exit 1
                    fi
                else
                    echo "/etc/apache2/apache2.conf found."
                fi

                echo "Checking configuration syntax..."
                sudo apache2ctl configtest || { echo "Apache configuration syntax error. Exiting."; exit 1; }
                
                echo "Enabling Apache to start on boot"
                sudo systemctl enable apache2

                echo "Starting Apache"
                sudo systemctl start apache2

                echo "Checking Apache status"
                sudo systemctl status apache2 || { echo "Apache failed to start. Check logs above."; exit 1; }

                echo "Installation and startup complete"
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
