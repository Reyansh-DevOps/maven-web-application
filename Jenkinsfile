node {

def mavenHome = tool name: "maven3.9.1"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))])

properties([pipelineTriggers([githubPush()])])

timestamps{
}
  
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
}
