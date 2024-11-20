pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        TF_VAR_region = 'us-east-1' // Example region
    }

    stages {
        stage('Initialize Environment') {
            steps {
                script {
                    // Ensure Terraform is installed
                    sh 'terraform --version || curl -o /tmp/terraform.zip https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip && unzip /tmp/terraform.zip -d /usr/local/bin'
                }
            }
        }

        stage('Terraform Init and Plan') {
            steps {
                script {
                    def terraformFolders = ['00-vpc', '10-sg', '20-bastion', '30-rds']
                    for (folder in terraformFolders) {
                        dir(folder) {
                            echo "Processing folder: ${folder}"
                            sh 'terraform init'
                            sh 'terraform plan -out=tfplan'
                        }
                    }
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                script {
                    def terraformFolders = ['00-vpc', '10-sg', '20-bastion', '30-rds']
                    for (folder in terraformFolders) {
                        dir(folder) {
                            echo "Applying Terraform in folder: ${folder}"
                            sh 'terraform apply -auto-approve tfplan'
                        }
                    }
                }
            }
        }

        stage('Post-Deployment Actions') {
            steps {
                echo 'Deployment Complete. Triggering Notifications.'
                // Add any post-deployment tasks like sending notifications.
            }
        }
    }

    post {
        always {
            echo 'Cleaning up temporary files...'
            sh 'rm -f */tfplan'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
