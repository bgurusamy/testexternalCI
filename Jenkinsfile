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
        stage('Delete old files')
         {
            steps 
             {
                 dir('/tmp/externalCI') {
                    deleteDir()
                     echo 'old files are deleted'
}
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
    git credentialsId: 'a7cb5dec-9051-40cb-942b-d8423f3be9bb', url: 'https://github.com/LableOrg/java-maven-junit-helloworld.git'

echo 'test cases are downloaded to the local folder'
} 
 }
}
         
          stage ('executing unit testing') {
            steps {
 dir('/tmp/externalCI') 
{  
      withMaven(maven:'maven_local') {

             
                             sh "mvn test"
           echo 'unit test cases are completed'
               }
                            }
            }
    
          }
         
          stage ('executing integration testing') {
            steps {
 dir('/tmp/externalCI') 
{  
      withMaven(maven:'maven_local') {

             
                             sh "mvn verify"
           echo 'integration test cases are completed'
               }
                            }
            }
    
          }
 
  stage ('executing the build') {
            steps {
 dir('/tmp/externalCI') 
{  
              withMaven(maven:'maven_local') {

             
                             sh "mvn clean install -Dmaven.skip.test=true"
               }
                            }
            }
      
  }
       stage ('archive the artificats') {
            steps {
                 dir('/tmp/externalCI') 
{ 
             // sh 'make'
              //  archiveArtifacts artifacts: '**/target/*.jar',
                    junit 'target/surefire-reports/**/*.xml' 
                 echo 'archiving the artificats is completed '
}
                }
     }
    
      stage('Quality Analysis') {
    steps {
          dir('/tmp/externalCI') 
{ 
              withSonarQubeEnv('sonarqube') {
                 withEnv(["JAVA_HOME=${ tool 'jdk8' }", "PATH+MAVEN=${tool 'maven_local'}/bin:${env.JAVA_HOME}/bin"]) {   
                sh 'mvn clean package sonar:sonar'
                 }
              }
              
    }
   }
      }
           stage ('deploy the artificats') {
            steps {
                 dir('/tmp/externalCI') 
{ 
             //local tomcat
                   sh ' cp target/java-maven-junit-helloworld-1.0-SNAPSHOT.jar  /Applications/apache-tomcat-9.0.8/lib/
              //remote tomcat
                    // sshagent(['CREDENTIALS_ID']) {
                     // sh 'scp target/java-maven-junit-helloworld-1.0-SNAPSHOT.jar lsauser@10.255.171.195:/opt/apache-tomcat-9.0.8/lib/'
                    //}
                 echo 'deploying the artificats is completed '
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
