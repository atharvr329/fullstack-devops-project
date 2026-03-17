pipeline {
agent any
environment {
    DOCKER_USER = "sonali10"
    KUBECONFIG = "/home/jenkins/.kube/config"
}

stages {
stage('Checkout') {
steps {
checkout scm
}
}

stage('Verify Repo') {
steps {
sh 'echo Repository cloned successfully'
sh 'ls -la'
}
}

stage('Build Backend') {
steps {
sh 'docker build -t $DOCKER_USER/backend:latest ./backend'
}
}

stage('Build Frontend') {
steps {
sh 'docker build -t $DOCKER_USER/frontend:latest ./frontend'
}
}

stage('Docker Login') {
steps {
withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
sh 'echo $PASS | docker login -u $USER --password-stdin'
}
}
}

stage('Push Images') {
steps {
sh 'docker push $DOCKER_USER/backend:latest'
sh 'docker push $DOCKER_USER/frontend:latest'
}
}

stage('Deploy to Kubernetes') {
steps {
sh '''
export KUBECONFIG=/home/jenkins/.kube/config
kubectl --server=https://host.docker.internal:49933 --insecure-skip-tls-verify get nodes
kubectl apply -f k8s/ --server=https://host.docker.internal:49933 --insecure-skip-tls-verify
'''
}
}

stage('Verify Deployment') {
steps {
sh '''
export KUBECONFIG=/home/jenkins/.kube/config
kubectl get pods
kubectl get svc
'''
}
}
}

}
