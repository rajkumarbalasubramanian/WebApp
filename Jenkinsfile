pipeline {
	agent any

	tools {
		maven "maven"
	}
	
	parameters { 
         string(name: 'tomcat-qa', defaultValue: '35.166.210.154', description: 'Staging Server')
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
   			sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat-qa}:/opt/tomcat/webapps/'
			}
			}
			}
}	
}

