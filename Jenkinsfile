pipeline{
    agent any;
    
    environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }
    
    stages{
        stage("Code"){
            steps{
                git url: "https://github.com/codebydeep/envy-ecommerce.git", branch: "main"
            }
        }
        stage("Build Stage"){
            steps{
                sh "docker build -t react-app:latest ."
            }
        }
        stage("Docker Login"){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: "dockerCredentialsID",
                    usernameVariable: "dockerHubUser",
                    passwordVariable: "dockerHubPass",
                    )]){
                    sh "docker image tag react-app:latest ${env.dockerHubUser}/react-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/react-app:latest"
                }
            }
        }
        stage("K8S Deployment"){
            steps{
                sh 'kubectl apply -f kubernetes/deployment.yml'
                sh 'kubectl apply -f kubernetes/service.yml'
                sh 'kubectl apply -f kubernetes/ingress.yml'
                sh 'kubectl apply -f kubernetes/hpa.yml'
            }
        }
    }
}
