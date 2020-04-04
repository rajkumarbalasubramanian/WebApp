pipeline {
	agent any
	
	tools {
		maven "maven"
	}
	
	parameters { 
         string(name: 'tomcat_qa', defaultValue: '18.191.168.82', description: 'QA Server')
         string(name: 'tomcat_prod', defaultValue: '18.220.138.54', description: 'Production Server')
    } 

stages {
	stage('Send notification') 
	{
		steps {
		 slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
		}
	}

 	
}

post 
	{
		always 
		{
			sendNotification(currentBuild.currentResult)
			
		}
   }
}  
   void sendNotification(buildResult) {
		if ( buildResult == "SUCCESS" ) {
   			 slackSend color: "good", message: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} was successful"
  		}
 		else if( buildResult == "FAILURE" ) { 
    			slackSend color: "danger", message: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} was failed"
  		}
  		else if( buildResult == "UNSTABLE" ) { 
    			slackSend color: "warning", message: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} was unstable"
  		}
  		else {
    			slackSend color: "danger", message: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} its resulat was unclear"	
  		     }
   	}
 

