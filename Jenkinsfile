//pipleline 
pipeline{
    
	tools {
	    maven 'maven3.9.0'
	    
	}
	
	options {
	    timestamps()
	    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')
	    
	}

	stages{
		//checkout code stage
		stage('checkoutcode'){
			steps{
			    sendSlackNotifications('START')
				git branch: 'development', credentialsId: '0471c15d-311b-4aa4-a1d0-f4514560ca4a', url: 'https://github.com/rnvishnu/maven-web-application.git'
			
			}
		}
		//Build Stage
		stage('Build'){
			steps{
				sh "mvn clean package"
			}
		}
		
		//Execute SonarQube Report
		stage('ExecuteSonarQubeReport'){
			steps{
				sh "mvn clean sonar:sonar"
			}
		}
		
}//stages close

post {
  success {
    sendSlackNotifications(currentBuild.result)
  }
  failure {
    sendSlackNotifications(currentBuild.result)
  }
}

}//Pipeline close
//sendSlackNotifications to #devopsjenkinsnotification
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
