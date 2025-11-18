stage('Build') {
    steps {
        script {
            // Define variables...
            def imageTag = "my-k8s-app:${env.BUILD_NUMBER}"
            
            // **NEW: Change directory to 'kube'**
            dir('kube') {
                // Now running from within the 'kube' folder.
                
                // 1. Build your Docker Image. 
                // Since we are inside 'kube', the Dockerfile is just 'Dockerfile'
                // and the build context is '.' (the current directory).
                bat "docker build -t my-k8s-app:${env.BUILD_NUMBER} ." 
                
                // 2. Tag and Push the image (ensure this uses your correct registry username)
                bat "docker tag ${imageTag} your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                bat "docker push your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
                
                env.IMAGE_TAG = "your-registry-username/my-k8s-app:${env.BUILD_NUMBER}"
            }
        }
    }
}

// NOTE: You must also update your Deploy stage to use dir('kube')
// to find the YAML files if they are not at the root.
stage('Deploy') {
    steps {
        script {
            // ...
            dir('kube') {
                // Commands here now run inside the 'kube' directory
                bat "sed -i 's|node-app-image-placeholder|${env.IMAGE_TAG}|g' node-app-deployment.yaml"
                bat 'kubectl apply -f node-app-deployment.yaml'
                bat 'kubectl apply -f node-app-service.yaml'
                // ...
            }
        }
    }
}