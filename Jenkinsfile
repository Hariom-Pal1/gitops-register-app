pipeline {
    agent { label "jenkins-slave" }
    
    // Yahan parameters define karna zaroori hai agar CI se trigger ho raha hai
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker Image Tag')
    }

    environment {
        APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps { cleanWs() }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Hariom-Pal1/gitops-register-app'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "Updating image to: ${APP_NAME}:${params.IMAGE_TAG}"
                    sed -i 's/${APP_NAME}:.*/${APP_NAME}:${params.IMAGE_TAG}/g' deployment.yaml
                    cat deployment.yaml
                """
            }
        }

        stage("Push to Git") {
            steps {
                script {
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                        sh """
                            git config user.name "Hariom-Pal1"
                            git config user.email "hariompal483@gmail.com"
                            git add deployment.yaml
                            
                            # Check if there are changes before committing
                            if git diff-index --quiet HEAD; then
                                echo "No changes detected, skipping commit."
                            else
                                git commit -m "Updated Deployment Manifest to ${params.IMAGE_TAG}"
                                # Correct Push URL
                                git push https://github.com/Hariom-Pal1/gitops-register-app.git main
                            fi
                        """
                    }
                }
            }
        }
    }
}
