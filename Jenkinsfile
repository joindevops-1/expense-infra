pipeline {
    agent any

    environment {
        
    }

    stages {
        

        stage('Terraform Init and Plan') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'us-east-1') {
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
        }

        stage('Terraform Apply') {
            steps {
                script {
                    def terraformFolders = ['00-vpc', '10-sg', '20-bastion', '30-rds']
                    for (folder in terraformFolders) {
                        dir(folder) {
                            echo "Applying Terraform in folder: ${folder}"
                            //sh 'terraform apply -auto-approve tfplan'
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
