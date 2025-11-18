
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                
                    def imageTag = "my-k8s-app:${env.BUILD_NUMBER}"
                    

                    bat "docker build -t ${imageTag} -f kube/Dockerfile ./kube"
                    
                   
                    bat "docker tag ${imageTag} your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                    bat "docker push your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                    
                    env.IMAGE_TAG = "your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running tests..."
                    // Add commands here to execute unit/integration tests (e.g., 'npm test')
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application with image tag: ${env.IMAGE_TAG}"
                    
                    // 1. Update the image tag in the Kubernetes deployment manifest
                    // This uses sed to find and replace the image string in the YAML file.
                    // This is only a temporary approach; for production, use Helm or Kustomize.
                    bat "sed -i 's|node-app-image-placeholder|${env.IMAGE_TAG}|g' kube/node-app-deployment.yaml"
                    
                    // 2. Apply the manifest files to the Kubernetes cluster
                    // Jenkins must have kubectl configured and credentials set up.
                    bat 'kubectl apply -f kube/node-app-deployment.yaml'
                    bat 'kubectl apply -f kube/node-app-service.yaml'
                    
                    // 3. Wait for the deployment to complete successfully
                    bat 'kubectl rollout status deployment/node-app-deployment'
                    
                    echo "Deployment to Kubernetes complete."
                }
            }
        }
    }
}