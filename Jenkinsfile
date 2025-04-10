pipeline {
    agent {
        label 'AGENT-1'
    }
    options{
        timeout(time: 30, unit: 'MINUTES')
    }
    parameters{
        booleanParam(name: 'deploy', defaultValue: true, description: 'select to deploy or not')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'prod'], description: 'select your env')
        string(name: 'version', description: 'Enter your app version')

    }
    environment {
        appVersion = '' // This will become global, we can use env accross stages
        region = 'us-east-1'
        account_id = '361769572747'
        project ='expense'
        environment = ''
        component = 'backend'
    }

    stages {

        stage('Setup Environment'){
            steps{
                script{
                    environment = params.ENVIRONMENT
                    appVersion = params.version
                }
            }
        }

        
        stage('Deploy'){
            when {
                expression { params.deploy }
            }
            steps{
                withAWS(region: 'us-east-1', credentials: 'aws-creds-terraform'){
                    sh """
                       aws eks update-kubeconfig --region ${region} --name ${project}-${environment}  
                       cd helm
                       sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${environment}.yaml
                       helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                    """
                }
                }

            }
        }
            
    post {
        always {
            echo "this section runs always"
            deleteDir()
        }
        success{
            echo "this section runs when pipeline success"
        }
        failure{
            echo "this section runs when pipeline failure"
        }
    }
}
