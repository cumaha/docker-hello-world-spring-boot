node('node01') {
    // reference to maven
    // ** NOTE: This 'maven' Maven tool must be configured in the Jenkins Global Configuration.  
    def app
    def mvnHome = tool 'maven'
    stage('Clone Repo') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/cumaha/docker-hello-world-spring-boot.git'
      // Get the Maven tool.
      // ** NOTE: This 'maven' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'maven'
    }    
  
    stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
    }
	
	stage('Publish Tests Results'){
      parallel(
        publishJunitTestsResultsToJenkins: {
          echo "Publish junit Tests Results"
		  junit '**/target/surefire-reports/TEST-*.xml'
		  archive 'target/*.jar'
        },
        publishJunitTestsResultsToSonar: {
          echo "This is branch b"
      })
    }
		
    stage('Build Docker Image') {
      // build docker image
      sh "mv ./target/hello*.jar ./data" 
      
      app = docker.build("mahamad/hello-world-java")
    }
   
    stage('Deploy Docker Image'){
      
      // deploy docker image to nexus
	  docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
    }
}
}
