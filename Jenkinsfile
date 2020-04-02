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
	    sh 'mvn clean package'
        }
    }
}
}
