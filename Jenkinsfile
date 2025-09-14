pipeline{
    agent{
        label 'LABEL_1'
    }
    environment {
        REGION = 'us-east-1'
        ACC_ID = 557690617909
        appVersion = ''
        PROJECT = "roboshop"
        COMPONENT = "cart"
    }
    options{
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters{
        string(name: 'appVersion', description: 'Image version of the application')
        choice(name: 'deploy_to', choices:['dev','qa','prod'], description: 'pick the value for environment')
    }
    stages{
        stage('Deploy'){
            steps{
                script{
                    withAWS(credentials: 'aws-auth', region: 'us-east-1'){
                        sh """
                            aws eks update-kubeconfig --region $REGION --name "$PROJECT-${params.deploy_to}"
                            kubectl get nodes
                            sed -i "s/IMAGE_VERSION/${params.appVersion}/g" values-${params.deploy_to}.yaml
                            helm upgrade --install $COMPONENT -f values-${params.deploy_to}.yaml -n $PROJECT .
                        """
                    }
                }
            }
        }
    }
}