pipeline {
    agent any
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('vcs') {
            agent { label 'build' }
            steps {
                git url: "https://github.com/satishnamgadda/saleorreact-storefront.git",
                    branch: "master"
            }
        }
        stage('build') {
            agent { label 'build' }
            steps {
                sh 'sudo chmod 777 /var/run/docker.sock'
                sh 'docker info'
                sh 'docker image build -t satish1990/saleorstorefront:1.0 .'
                sh 'docker image push satish1990/saleorstorefront:1.0'
            }
        }
        stage('terraform') {
            agent { label 'terraform' }
            steps {
                git url: "https://github.com/hashicorp/learn-terraform-provision-eks-cluster",
                    branch: "main"
                sh 'terraform -chdir=/home/ubuntu/remote_root/workspace/salerstorefront init' 
                sh 'ls' 
                sh 'terraform -chdir=/home/ubuntu/remote_root/workspace/salerstorefront apply -auto-approve'
				//sh 'terraform destroy -auto-approve'
                sh 'aws eks --region $(terraform -chdir=/home/ubuntu/remote_root/workspace/salerstorefront output -raw region) update-kubeconfig \
                     --name $(terraform -chdir=/home/ubuntu/remote_root/workspace/salerstorefront output -raw cluster_name)'
                  
            }
        }
        stage('k8s') {
            agent { label 'terraform' }
            steps {
                sh 'kubectl apply -f saleorfront'
            }
        }
    }
}