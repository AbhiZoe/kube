
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Define variables for image name and tag
                    def imageTag = "my-k8s-app:${env.BUILD_NUMBER}"
                    
                    // 1. Build your Docker Image (replace 'my-k8s-app' with your image name)
                    // The '.' at the end specifies the context (where the Dockerfile is located)
                    sh "docker build -t ${imageTag} ."
                    
                    // 2. Tag and Push the image to a registry (e.g., Docker Hub or GCR)
                    // You must be authenticated to the registry on the Jenkins agent.
                    sh "docker tag ${imageTag} your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                    sh "docker push your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                    
                    // Store the image tag for use in the Deployment stage
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
                    sh "sed -i 's|node-app-image-placeholder|${env.IMAGE_TAG}|g' kube/node-app-deployment.yaml"
                    
                    // 2. Apply the manifest files to the Kubernetes cluster
                    // Jenkins must have kubectl configured and credentials set up.
                    sh 'kubectl apply -f kube/node-app-deployment.yaml'
                    sh 'kubectl apply -f kube/node-app-service.yaml'
                    
                    // 3. Wait for the deployment to complete successfully
                    sh 'kubectl rollout status deployment/node-app-deployment'
                    
                    echo "Deployment to Kubernetes complete."
                }
            }
        }
    }
}