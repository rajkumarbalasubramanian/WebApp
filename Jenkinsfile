pipeline {
	agent any

	tools {
		maven "maven"
	}

stages {
    stage('Clone sources') {
	git url: 'https://github.com/ashishgupta861/webapp.git'
    }
    
    stage('Build Project') {
                sh 'mvn clean package'
        }
}
}
