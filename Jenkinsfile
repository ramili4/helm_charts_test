pipeline {
    agent any

    environment {
        SOURCE_HELM_REPO = 'git@github.com:ramili4/helm_charts_test.git'
        TARGET_HELM_REPO = 'git@github.com:ramili4/tested_charts.git'
        CHART_VERSION = '1.0.0'
        GIT_CREDENTIALS_ID = 'github-ssh-key' // ID of the SSH key in Jenkins
        HELM_CHART_DIR = '/var/jenkins_home/workspace/Helm_git'
    }

    stages {
        stage('Checkout Source') {
            steps {
                // Checkout the source repository where Helm charts are stored
                git branch: 'main', credentialsId: "${GIT_CREDENTIALS_ID}", url: "${SOURCE_HELM_REPO}"
            }
        }

        stage('Test Helm Chart') {
            steps {
                script {
                    // Test the Helm chart
                    dir("${HELM_CHART_DIR}") {
                        sh 'helm lint .'
                        
                    }
                }
            }
        }

        stage('Package Helm Chart') {
            steps {
                script {
                    dir("${HELM_CHART_DIR}") {
                        sh "helm package . --version ${CHART_VERSION}"
                    }
                }
            }
        }

        stage('Push to Target Repo') {
            steps {
                script {
                    // Clone the target repository
                    sh "git clone ${TARGET_HELM_REPO} target-repo"
                    
                    // Copy the packaged Helm chart to the target repository
                    sh "cp ${HELM_CHART_DIR}/*.tgz target-repo/"
                    
                    // Commit and push the changes
                    dir('target-repo') {
                        sh '''
                            git config user.email "jenkins@example.com"
                            git config user.name "Jenkins"
                            git add .
                            git commit -m "Add packaged Helm chart version ${CHART_VERSION}"
                            git push origin main
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Helm chart successfully tested, packaged, and pushed to target repository!'
        }
        failure {
            echo 'Failed to test, package, or push Helm chart.'
        }
    }
}
