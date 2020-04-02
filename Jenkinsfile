node {

    stage('Clone sources') {
	git poll: true,
       	    url: 'https://github.com/ashishgupta861/webapp.git'
    }
    
    stage('Build Project') {
                sh 'mvn clean package'
        }
}
	 
