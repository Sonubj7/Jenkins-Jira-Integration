pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 echo 'Building...'
             }
             post {
                 always {
                     jiraSendBuildInfo site: 'XXXXXXXX', branch: 'EM-2'
                 }
             }
         }
         stage('Deploy - Staging') {
             steps {                 
                 jiraSendDeploymentInfo ( site: 'XXXXX', environmentId: 'STG', environmentName: 'stg', environmentType: 'staging', state: 'in_progress')
                       sh 'echo Deploying to Staging started..'
                          withCredentials([file(credentialsId: 'BoxDrop-Credentials', variable: 'secretFile')]) {
                       // do something with the file, for instance 
                       sh 'sudo scp $secretFile .'
                       sh "sudo chmod +x -R ${env.WORKSPACE}"
                       sh 'sudo yarn build'
                       sh "sudo scp -r * /var/www/html/ "
                       sh "sudo npm install -g pm2"
                       sh 'sudo yarn global add pm2'
                       sh 'sudo pm2 start yarn --name "dcc-webapp" -- start'
                       sh 'sudo pm2 box-app'
                              }       
             }
             post {
                 always {
                   sh 'sleep 2'
                   sh 'echo Deployment to production finished'
                   jiraComment body: 'This message is from jenkins. \n KINDLY VIEW YOUR DEPLOYMENT TRACKER FOR  MORE DETAILS', issueKey: 'EM-2'       
                             }
             success {
                echo 'Deployment successful'
                jiraSendDeploymentInfo (
                    environmentId: 'STG', 
                    environmentName: 'stg',  
                    site: 'XXXXXX', 
                    environmentType: 'staging',
                    state: 'successful'
                )
              }
              failure {
                echo 'Deployment failed'
                jiraSendDeploymentInfo (
                    environmentId: 'STG', 
                    environmentName: 'stg', 
                    environmentType: 'staging', 
                    site: 'XXXXXX', 
                    state: 'failed'
                )
              }
              aborted {
                echo 'Deployment cancelled'
                jiraSendDeploymentInfo (
                    environmentId: 'STG', 
                    environmentName: 'stg', 
                    environmentType: 'staging', 
                    site: 'XXXXXX', 
                    state: 'cancelled'
                )
              }
            }
          }
        }
}  
