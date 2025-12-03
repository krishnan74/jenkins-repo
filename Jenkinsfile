pipeline {
    agent any

    environment {
        // Jenkins Secret Credentials
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
        AWS_SESSION_TOKEN     = credentials('aws-session-token')
        AWS_DEFAULT_REGION    = "us-east-1"
    }

    triggers {
        // run on every 2 minutes
        pollSCM('H/2 * * * *')
    }

    stages {

        // stage('Checkout') {
        //     steps {
        //         git branch: "${env.GIT_BRANCH}", url: 'https://github.com/krishnan74/jenkins-repo'
        //     }
        // }

        stage('Terraform Init') {
            steps {
                sh """
                terraform --version
                terraform init
                """
            }
        }

        stage('Terraform Plan') {
            steps {
                sh """
                terraform plan -out=tfplan
                """
            }
        }

        stage('Approval') {
            when {
                anyOf {
                    branch 'main'
                    branch 'master'
                }
            }
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    input message: "Approve Terraform Apply?"
                }
            }
        }

        stage('Terraform Apply') {
            when {
                anyOf {
                    branch 'main'
                    branch 'master'
                }
            }
            steps {
                sh """
                terraform apply -auto-approve tfplan
                """
            }
        }
    }

    post {
        success {
            echo "Terraform pipeline completed successfully!"
        }
        failure {
            echo "Terraform pipeline failed."
        }
    }
}
