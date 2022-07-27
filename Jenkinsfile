node
{
    def mavenHome = tool name: "maven3.8.6"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '2', numToKeepStr: '2')), pipelineTriggers([pollSCM('* * * * *')])])
    echo "The branch name is: ${env.BRANCH_NAME}"
    echo "The build number is: ${env.BUILD_NUMBER}"
    echo "The node name is: ${env.NODE_NAME}"
    echo "The path to the workspace is : ${env.WORKSPACE}"
    
   try{  
       slackNotification("STARTED")
    stage('CheckoutCode'){
    git branch: 'development', credentialsId: '79c91c23-1539-45cc-9145-14ea9a699d20', url: 'https://github.com/trainde/maven-web-application.git'
    }
    
    stage('Build'){
       sh "${mavenHome}/bin/mvn clean package"
    }    
   
  
      stage ('ExecuteSonarqubeReport'){
      sh "${mavenHome}/bin/mvn clean sonar:sonar"
    }  
    
    stage('UploadArtifactIntoRepo'){
      
    sh "${mavenHome}/bin/mvn deploy"
    }

  
   
    stage('DeploytoTomcatServer'){
    sshagent(['dc7444aa-03b2-4cc8-b718-06f764e8438a']){
        sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.88.179.168:/opt/apache-tomcat-9.0.65/webapps/"
    }
    }
   }
   catch (e){
    currentBuild.result = "FAILURE"
    throw e
        }
finally{
    slackNotification (currentBuild.result) 
       }

}// node closing
   


def slackNotification(String buildStatus = 'STARTED') {
  
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'BLACK'
    colorCode = '#000000'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
    
    
    
