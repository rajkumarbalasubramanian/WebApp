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

    stage('Clone sources') 
	{
	    steps {
		    git url: 'https://github.com/ashishgupta861/webapp.git'
    		}
    }
    
    stage('Build Project') 
	{
	    steps {    
			sh 'mvn -DskipTests=True clean package'
        }
    }
	
	stage("Static Code Analysis")
	{
		
		steps 
		{
			withSonarQubeEnv('sonarstatic') 
			{
                sh 'mvn sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
            }
		}
	}

	stage('Deploy to QA') 
	{
		steps 
		{
			sshagent(['tomcat-qa']) 
			{
				sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_qa}:/opt/tomcat/webapps/"
			}
		}
	}
	
	stage('Artifactory configuration') 
	{
		steps 
		{
			script 
			{
				// Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    			def server = Artifactory.server "artifactory"
    			
				// Create an Artifactory Maven instance.
				def uploadSpec = 
				"""{
					"files": 
					[
						{
							"pattern": "target/*.war",
							"target": "libs-release-local/"
						}
					]
				}"""
				server.upload(uploadSpec)
			}
		}
	}
	
	stage('Deploy to prod') 
	{
		steps 
		{
			sshagent(['tomcat-prod']) 
			{
				sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_prod}:/opt/tomcat/webapps/"
			}
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
   			 slackSend color: "good", message: "SUCCESSFUL: Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER}"
  		}
 		else if( buildResult == "FAILURE" ) { 
    			slackSend color: "danger", message: "FAILED: Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER}"
  		}
  		else if( buildResult == "UNSTABLE" ) { 
    			slackSend color: "warning", message: "UNSTABLE: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER}"
  		}
  		else {
			slackSend color: "danger", message: "${buildResult}: Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER}"	
  		     }
   	}
 

