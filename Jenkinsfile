pipeline {
	agent any

	tools {
		maven "maven"
	}
	
	parameters { 
         string(name: 'tomcat_qa', defaultValue: '3.135.204.131', description: 'QA Server')
         string(name: 'tomcat_prod', defaultValue: '18.188.183.63', description: 'Production Server')
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
	
	
	stage("Static Code Analysis"){
		steps {
		withSonarQubeEnv('sonarstatic') {
                sh 'mvn sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
              }
		
		
	}
	
	}
	
	stage('Deploy to QA') {
		steps {
			sshagent(['tomcat-qa']) {
				sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_qa}:/opt/tomcat/webapps/"
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
		server.upload(uploadSpec)
			}
    }
}
	stage('Deploy to prod') {
		steps {
			sshagent(['tomcat-prod']) {
				sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_prod}:/opt/tomcat/webapps/"
			}
			}
			}
}
}

