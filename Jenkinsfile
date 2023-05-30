pipeline{
    agent any

    parameters{
        choice ( name: 'action', choices: [ 'create' , 'destroy' ] , description: 'Do you want to create or destroy deployment')
        string(name: 'cluster_name' , defaultValue: 'myEKS' , description: 'EKS cluster name')
        string(name: 'aws_region' , defaultValue: 'ap-northeast-1' , description: 'EKS cluster region')
    }
    stages{
        stage("Git checkout"){
            steps{
                echo "========executing git checkout========"

                git branch: 'main', url: 'https://github.com/latesh-11/task-2-deployment.git'
            }
        }
         stage("eks_connect"){
            steps{
                echo "========executing eks_connect========"

                script{
                    sh """
                        aws configure set aws_access_key_id "${env.AWS_ACCESS_KEY_ID}"
                        aws configure set aws_secret_access_key "${env.AWS_SECRET_ACCESS_KEY}"
                        aws configure set region "${params.aws_region}"
                        aws eks --region "${params.aws_region}" update-kubeconfig --name "${params.cluster_name}"
                        """
                }
            }
        }
        stage("eks_deloy"){
            when { 
                expression { 
                    params.action == 'create' 
                } 
            }
            steps{
                echo "========executing eks deploy========"
               
                script{
                    def apply = false
                    try{
                        input massage: 'please confirm the apply to initita the deployments', ok: 'Ready to apply the config'
                        apply = true
                    }
                    catch(err){
                        apply = false
                        CurrentBuild.result = 'UNSTABLE'
                    }
                    if(apply){
                       sh """ 
                        pwd
                        cp /var/lib/jenkins/bin/kubectl /usr/bin
                        chmod -X ./kubectl
                        ls -la
                        ./kubectl apply -f .
                        ./kubectl get all 
                         """
                    }
                }
            }
        }
        stage("delete deployment"){
            when {expression {params.action == 'destroy'}}
            steps{
                echo "========executing delete deployment========"
                
                script {
                    def destroy = falce

                    try{
                        input massage: 'please confirm the destroy to delete the deployments' , ok: 'Ready to destroy the config'
                        destroy = true
                    }
                    catch(err){
                        destroy = false
                        CurrentBuild.result= 'UNSTABLE'
                    } 
                    if(destroy){
                        sh """
                            cp /var/lib/jenkins/bin/kubectl 
                            ls -la
                            ./kubectl delete -f .
                            """
                    }
                }
            }
        }
    }
}