node{
echo "Build Number is: ${env.BUILD_NUMBER}"
echo "Build ID is: ${env.BUILD_ID}"
echo "Build Job Name is: ${env.JOB_NAME}"
echo "Jenkins Home is: ${env.JENKINS_HOME}"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
	
	def mavenHome = tool name: "maven3.9.0"
	
	try
	{
		stage('Checkout Code'){
		sendSlackNotifications('START')
		git branch: 'development', credentialsId: '0471c15d-311b-4aa4-a1d0-f4514560ca4a', url: 'https://github.com/rnvishnu/maven-web-application.git'
		}
		stage('Build') {
		sh "${mavenHome}/bin/mvn clean package"
		}
/*
		stage('ExecuteSonarQubeReport'){
		sh "${mavenHome}/bin/mvn clean sonar:sonar"
		}
		stage('UploadArtifactIntoNexus'){
			sh "${mavenHome}/bin/mvn clean deploy"
		}
		stage('DeployAppIntoTomcatServer'){
			deploy adapters: [tomcat9(credentialsId: '139bf02f-f6d2-4e28-ad4d-df38df249a7a', path: '', url: 'http://3.128.204.156:8080/')], contextPath: null, war: '**/*maven-web-application.war'
		} */
	}

	catch(e){
	currentBuild.result = "FAILED"
	throw e
	}
	finally{
	sendSlackNotifications(currentBuild.result)
	}	

}

//Send Slack Notificatons

def sendSlackNotifications(String buildStatus = 'START') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'START') {
    colorName = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: '#devopsjenkinsnotification')
}
