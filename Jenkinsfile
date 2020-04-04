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
   void sendNotification(buildResult){
		echo buildResult
   }
 

