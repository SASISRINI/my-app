node{
   stage('SCM Checkout'){
     git 'https://github.com/SASISRINI/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'sasisri', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis 2') {
	        def mvnHome =  tool name: 'sasisri', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Nexus Image Push in nexus2'){
   sh "docker login -u admin -p nexus123 13.233.25.38:8084"
   sh "docker tag sasisri/myweb:0.0.2 13.233.25.38:8084/damo:1.0.0"
   sh 'docker push 13.233.25.38:8084/damo:1.0.0'
   }
   stage('Build Docker Imager'){
   sh 'docker build -t sasisri/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u sasisri -p ${dockerPassword}"
    }
   sh 'docker push sasisri/myweb:0.0.2'
   }
   
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest sasisri/myweb:0.0.2' 
   }
}
