pipeline {
	agent any
	
	tools {
		maven "maven"
	}
	
	parameters { 
         string(name: 'tomcat_qa', defaultValue: '18.189.192.112', description: 'QA Server')
         string(name: 'tomcat_prod', defaultValue: '3.15.186.172', description: 'Production Server')
	 string(name: 'jiraId', defaultValue: 'DEV-1', description: 'Jira ID')
		
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
	
	//stage("Static Code Analysis")
	//{
		
	//	steps 
	//	{
	//		withSonarQubeEnv('sonarstatic') 
	//		{
          //      sh 'mvn sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
            //}
		//}
	//}

	stage('Deploy to QA') 
	{
		steps 
		{
			sshagent(['tomcat-qa']) 
			{
				sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_qa}:/opt/tomcat/webapps/QAWebapp.war"
			}
		}
	}
	
	stage('Functional test') {
		steps {
			sh 'mvn -f functionaltest/pom.xml test'
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
	
	//stage('Performance test') {
	//	steps {
	//		blazeMeterTest credentialsId: 'perf', testId: '7890823.taurus', workspaceId: '474121'
	//		}
	//	}

	stage('Deploy to prod') 
	{
		steps 
		{
			sshagent(['tomcat-prod']) 
			{
				sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_prod}:/opt/tomcat/webapps/ProdWebapp.war"
			}
		}
	}
	
	stage('waitForProdDeploy'){
		steps {
			sh 'sleep 30000'
		}
	}
	stage('Sanity test') {
		steps {
			sh 'mvn -f Acceptancetest/pom.xml test'
			}
		}
	
	stage('send jira comment on completion') {
		steps {
			jiraAddComment comment: "Build and deployment completed: ${env.BUILD_NUMBER}", idOrKey: "${params.jiraId}", site: 'jiraDeploy'
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
    			slackSend color: "warning", message: "UNSTABLE: Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER}"
  		}
  		else {
			slackSend color: "danger", message: "${buildResult}: Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER}"	
  		     }
   	}
 

