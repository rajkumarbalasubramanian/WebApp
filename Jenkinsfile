pipeline {
	agent any

	tools {
		maven "maven"
	}
	
	parameters { 
         string(name: 'tomcat-qa', defaultValue: '3.133.120.68', description: 'QA Server')
         string(name: 'tomcat-prod', defaultValue: '34.209.233.6', description: 'Production Server')
    } 

    
stages {
    stage('Clone sources') {
	    steps {
		    git url: 'https://github.com/ashishgupta861/webapp.git'
    		}
    }
    
    stage('Build Project') {
	    steps {    
	    sh 'mvn -DskipTests=True clean package'
        }
    }
	stage('Deploy to QA') {
		steps {
			sshagent(['tomcat-qa']) {
   			sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.135.204.131:/opt/tomcat/webapps/'
			}
			}
			}
	
stage('Artifactory configuration') {
		steps {
			script {
			// Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    			def server = Artifactory.server "artifactory"
    			
			// Create an Artifactory Maven instance.
   			def uploadSpec = """{
  			"files": [
    			{
      				"pattern": "target/*.war",
      				"target": "libs-release-local/"
    			}
 			]
		}"""
		server.upload(uploadspec)
			}
    }
}	
}
	
	
}

