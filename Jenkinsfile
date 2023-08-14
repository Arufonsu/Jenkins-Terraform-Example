pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('checkout') {
            steps {
                checkout scm
            }
        }
        stage('tfsec') {
            steps {
                script {
                    // Run tfsec using Docker
                    try {
                        sh 'docker run --rm -v "$(pwd):/src" aquasec/tfsec .'
                    } catch (Exception e) {
                        currentBuild.result = 'UNSTABLE'
                        error("tfsec failed: ${e.message}")
                    }
                }
            }
        }
        stage('Approval for Terraform') {
            steps {
                input(message: 'Approval required before Terraform', ok: 'Proceed', submitterParameter: 'APPROVER')
            }
        }
        stage('terraform') {
            steps {
                script {
                    // Run terraform using Docker
                    try {
                        sh 'docker run --rm -v "$(pwd):/src" hashicorp/terraform apply -auto-approve -no-color'
                    } catch (Exception e) {
                        currentBuild.result = 'UNSTABLE'
                        error("Terraform apply failed: ${e.message}")
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
