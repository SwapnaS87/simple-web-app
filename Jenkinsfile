pipeline {
   
    agent any 

    tools {
         maven  "Maven"
    }

    stages {

          stage("Build") {
                 steps {         
		      echo "Building an applicaction using maven"
                      sh 'mvn clean package'	
                 }
                 post {
	                     success {
                              echo "Artifact is generated successfully."
							  echo "Archiving an artifact"
                              archiveArtifacts artifacts: '**/*.war', followSymlinks: false
                         }
                         failure {
							echo "Failed to generate an artifact"
                         }
                 }
          } 

          stage("Deploy to Staging and perform code analysis") {
			parallel {
		
			stage("Deploy to staging") {
				steps {
				   deploy adapters: [tomcat8(credentialsId: 'f6c8fecb-0eeb-4098-b0d3-75567d5f80bd', path: '', url: 'http://ec2-52-14-22-67.us-east-2.compute.amazonaws.com:8888/')], contextPath: null, onFailure: false, war: '**/*.war'
				}
				post {
				    success {
						echo "Application is deployed successfully on Tomcat in stanging environment"
					}
					failure {
						echo "Failed to deploy an application on Tomcat in staging environment"
					}
				}
			}
			
			stage("Perform code analysis") {
				steps {
				   sh 'mvn checkstyle:checkstyle'			   
				}
				post {
					success {
						checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
					}
				}
			}
		}
	}
	
	
	stage("Deploy to production") {
		steps {
		    timeout(time: 1, unit: 'DAYS') {
				input 'Do you want to deploy an application to production?'
			}
			
			deploy adapters: [tomcat8(credentialsId: 'f6c8fecb-0eeb-4098-b0d3-75567d5f80bd', path: '', url: 'http://ec2-52-14-22-67.us-east-2.compute.amazonaws.com:9999/')], contextPath: null, onFailure: false, war: '**/*.war'			
		}
		post {
			success {
				echo "Application is deployed successfully on Tomcat in production environment"
			}
			failure {
				echo "Failed to deploy an application on Tomcat in production environment"
			}
	    }		
    }
			  
 }
	
	
}