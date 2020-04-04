
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "jfrogartifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    rtMaven.tool = "maven"
    currentBuild.result = "SUCCESS"
    parameters { 
         string(name: 'tomcat_qa', defaultValue: '18.191.168.82', description: 'QA Server')
         string(name: 'tomcat_prod', defaultValue: '18.220.138.54', description: 'Production Server')
    } 

try {
stages {
   stage("notify") {
        slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
   stage('Clone sources') {
        git url: 'https://github.com/rajkumarbalasubramanian/WebApp.git'
   }
   stage("Static Code Analysis"){
 	withSonarQubeEnv('sonarstatic') {
        sh 'mvn sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
	         } 
   }
   stage('Maven Compile') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean compile'
   }
   stage('Build Project') {
	 rtMaven.run pom: 'pom.xml', goals: 'clean package' 
   }
   stage('Deploy to QA') {
	   sshagent(['tomcat-qa']) {
	   sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_qa}:/opt/tomcat/webapps/"
	   }
   }
   stage('Artifactory configuration') {
	 // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }
    stage('Deploy to prod') {
	sshagent(['tomcat-prod']) {
	sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@${params.tomcat_prod}:/opt/tomcat/webapps/"
	}
    }
    stage('performance test') {
	blazeMeterTest credentialsId: 'perf', testId: '7889218.taurus', workspaceId: '474121'
	}
    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
}	
catch (err) {
        currentBuild.result = "FAILURE"
        throw err
}
}
	 
