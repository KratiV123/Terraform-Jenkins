pipeline {
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    agent any
    stages {
        stage('Checkout') {
            steps {
                dir("git") {
                    sh "git clone https://github.com/KratiV123/Terraform-Jenkins.git"
                }
            }
        }
        stage('Plan') {
            steps {
                dir("git/terraform") {
                    sh 'terraform init'
                    sh 'terraform plan -out tfplan'
                    sh 'terraform show -no-color tfplan > tfplan.txt'
                }
            }
        }
        stage('Approval') {
            when {
                not {
                    equals expected: true, actual: params.autoApprove
                }
            }
            steps {
                script {
                    def plan = readFile 'git/terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }
        stage('Apply') {
            steps {
                dir("git/terraform") {
                    sh 'terraform apply -input=false tfplan'
                }
            }
        }
    }
}
