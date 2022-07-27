node
{
    def mavenHome = tool name: "maven3.8.6"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '2', numToKeepStr: '2')), pipelineTriggers([pollSCM('* * * * *')])])
    echo "The branch name is: ${env.BRANCH_NAME}"
    echo "The build number is: ${env.BUILD_NUMBER}"
    echo "The node name is: ${env.NODE_NAME}"
    echo "The path to the workspace is : ${env.WORKSPACE}"
    
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
    
    
    
