node {

def mavenHome = tool name: "maven3.9.1"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))])

properties([pipelineTriggers([githubPush()])])

timestamps{
}
try{
slackNotifications('STARTED')  
stage ('CheckoutCode'){
git branch: 'development', credentialsId: '9f8d5fae-236b-4e55-9783-09e5d764c446', url: 'https://github.com/Reyansh-DevOps/maven-web-application.git'
}

stage ('MavenBuild'){
sh "${mavenHome}/bin/mvn clean package"
}

stage ('ExicuteSonarQubeReport'){
sh "${mavenHome}/bin/mvn clean sonar:sonar"
}

stage ('UploadArtifactsIntoArtifactoryRepo'){
sh "${mavenHome}/bin/mvn clean deploy"
}

stage ('DeployAppIntoTomcat'){
sshagent(['ba435145-a02f-40ba-b935-ff9e17010f67']){
sh "scp -o StrictHostKeyChecking=no  target/maven-web-application.war ec2-user@172.31.28.185:/opt/apache-tomcat-9.0.73/webapps"  
}
}
}//try block closing
catch (e){
currentBuild.result = "FAILED"
throw e
}
finally{
slackNotifications(currentBuild.result)
}  
  
} //node closing

def slackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'
  //buildStatus = buildStatus ? "SUCCESS":"FAILURE"

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    colorName = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notificati
  slackSend (color: colorCode, message: summary, channel: "#general")
}
