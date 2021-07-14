node{
   stage('SCM Checkout'){
     git 'https://github.com/vendhanp/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Image'){
   sh 'docker build -t vendhanp/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u vendhanpv -p ${dockerPassword}"
    }
   sh 'docker push vendhanpv/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 18.191.128.155:8084"
   sh "docker tag vendhanpv/myweb:0.0.2 18.191.128.155:8084/vendhanpv:1.0.0"
   sh 'docker push 18.191.128.155:8084/vendhanpv:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exceptions
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest vendhanp/myweb:0.0.2' 
   }
}
}
