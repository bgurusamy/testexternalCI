pipeline {
    agent any 
       //different stages are provided  below 
     stages {
    
          // to send initial slack notification
       stage('send initial notification') {
            steps {
                slackSend channel: '#visetest', color: 'good', message: "STARTED: '${JOB_NAME} ${BUILD_NUMBER}' ",baseUrl:'https://cim.slack.com/services/hooks/jenkins-ci/',teamDomain:'cim',token:'ldhDuSiSkvnLEeFLUPyWndJF'
            }
        }
        
        
        stage('create custom work space') 
{
steps
{
sh 'mkdir /tmp/externalCI'  
echo 'custom work space is created'
}
}
  
 // Stage to check out the testcases from source repb
stage('checkout test cases')
   {
    steps{
dir('/tmp/externalCI') 
{  
//checkout scm
echo 'test cases are downloaded to the local folder'
} 
 }
}
 
  stage ('Build') {
            steps {
 dir('/tmp/externalCI') 
{  
              withMaven(maven:'maven_local') {

             
                             sh "mvn clean install"
               }
                             }
            post {
                success {
                 echo 'test success'
                    //junit 'target/surefire-reports/**/*.xml' 
                }
        
        
        }
    }
    }
      stage('Quality Analysis') {
    steps {
              withSonarQubeEnv('sonarqube') {
                 withEnv(["JAVA_HOME=${ tool 'jdk8' }", "PATH+MAVEN=${tool 'maven_local'}/bin:${env.JAVA_HOME}/bin"]) {   
                sh 'mvn clean package sonar:sonar'
                 }
              
    }
   }
      }
    
            //  to send  final slack notification
     stage('send final notification') {
            steps {
                slackSend channel: '#visetest', color: 'good', message: "COMPLETED: '${JOB_NAME} ${BUILD_NUMBER}' ",baseUrl:'https://cim.slack.com/services/hooks/jenkins-ci/',teamDomain:'cim',token:'ldhDuSiSkvnLEeFLUPyWndJF'
            }
        }
}
    //  post actions
    post
    {
     success
        {
     emailext  subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], to: 'Balachandar_gurusamy@cable.comcast.com', body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
       <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>"""

        }
        failure
        {
     emailext  subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], to: 'Balachandar_gurusamy@cable.comcast.com', body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
       <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>"""

        }
    }
        }
