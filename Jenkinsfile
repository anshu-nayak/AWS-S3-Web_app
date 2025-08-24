pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "ap-south-1"
        TF_DIR = "/home/ec2-user/AWS-Terraform" // Local path where Terraform repo will be cloned
    }

    stages {
        stage('Code Pull') {
            steps {
                // Pull website repo
                git branch: 'main', url: 'https://github.com/anshu-nayak/AWS-S3-Web_app.git'
                
                // Pull terraform repo
                dir("${TF_DIR}") {
                    git branch: 'main', url: 'https://github.com/anshu-nayak/AWS-Terraform.git'
                }
            }
        }

        stage('Terraform Init & Plan') {
            steps {
                dir("${TF_DIR}") {
                    sh '''
                    terraform init
                    terraform validate
                    terraform plan -out=tfplan
                    '''
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir("${TF_DIR}") {
                    sh 'terraform apply -auto-approve tfplan'
                }
            }
        }

        stage('Deploy Website Code') {
            steps {
                sh '''
                aws s3 sync . s3://aws-project-website-code-bucket --delete                   --exclude ".git/*"                   --exclude "Jenkinsfile"
                '''
            }
        }
    }
}