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
                sh 'docker image build -t satish1990/saleorcore:dev-24122022 .'
                sh 'docker image push satish1990/saleorcore:dev-24122022'
            }
        }
        stage('terraform') {
            agent { label 'terraform' }
            steps {
                git url: "https://github.com/hashicorp/learn-terraform-provision-eks-cluster",
                    branch: "main"
                sh 'terraform -chdir=/home/ubuntu/remote_root/workspace/salercore init' 
                sh 'ls' 
                sh 'terraform apply -auto-approve'
				//sh 'terraform destroy -auto-approve'
                
                  
            }
        }
    }
}