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
   			sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.221.43.94:/opt/tomcat/webapps/'
			}
			}
			}
}	
}

