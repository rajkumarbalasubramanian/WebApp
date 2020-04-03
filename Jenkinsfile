pipeline {
	agent any

	tools {
		maven "maven"
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
			sshagent(['tomcatenew']) {
   			sh 'scp -i -o StrictHostKeyChecking=no target/*.war ubuntu@3.133.120.68:/opt/tomcat/webapps/'
			}
			}
			}
}	
}

