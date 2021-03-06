pipeline {
    agent any 
    stages {
        stage ("Packer Build") {
            steps {
                sh """export AWS_PROFILE='sandbox'
                cd packer
                aws-profile packer build nginx.json
                cd ../"""
            }
        }
        stage ("Terraform") {
            steps {
                sh """
                export AWS_PROFILE='sandbox' 
                cd terraform 
                aws-profile terraform init 
                aws-profile terraform plan 
                aws-profile terraform apply -auto-approve
                """ 
            }
        }
        stage ("Deploy") {
            steps {
                sh """
                export AWS_PROFILE='sandbox' 
                export AWS_DEFAULT_REGION=eu-west-2
                IPADDRESS=\$(aws-profile /var/lib/jenkins/.local/bin/aws ec2 describe-instances --filters "Name=tag:Name,Values=JonnyWeb" | grep PublicIpAddress | awk -F ":" '{print \$2}' | sed 's/[",]//g' | xargs)
                ssh -o StrictHostKeyChecking=no ubuntu@\$IPADDRESS 'sudo rm -f /var/www/html/*'
                scp -o StrictHostKeyChecking=no deploy/index.html ubuntu@\$IPADDRESS:./index.html 
                ssh -o StrictHostKeyChecking=no ubuntu@\$IPADDRESS 'sudo mv ~/index.html /var/www/html/'
                """
            }
        }
    }
}

//Tell it to use sandbox profile
//navigating to directory within automation_test project
//initiliasing terraform, and using aws-profile to give terraform the rights it needs to access HMRCs AWS.
//dry run
//running the terraform files, and autoapproving the yes prompt