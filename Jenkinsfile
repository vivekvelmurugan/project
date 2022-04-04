node{
   stage('SCM Checkout'){
     git 'https://github.com/vivekvelmurugan/project.git'
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
   sh 'docker build -t vivekvel/project:0.1 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u vivekvel -p ${dockerPassword}"
    }
   sh 'docker push vivekvel/project:0.1'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 52.66.240.22:8083"
   sh "docker tag vivekvel/project:0.1 52.66.240.22:8083/vivek:1.0"
   sh 'docker push 52.66.240.22:8083/vivek:1.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
    }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest vivekvel/project:0.1'
   }
}
