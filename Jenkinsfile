pipeline {
	    agent any 	
		environment {
		PROJECT_ID = 'devops-267217'
		CLUSTER_NAME = 'cluster-1'
		LOCATION = 'us-central1-c' 
		CREDENTIALS_ID = 'k8master' 
		}
		
	    stages {	
		   stage('Scm Checkout') {            
			steps {
	                  checkout scm
			}	
	           }
	           
		   stage('Build') { 
	                steps {
	                  echo "Cleaning and packaging..."
	                  sh 'mvn clean package'		
	                }
	           }
		   stage('Test') { 
			steps {
		          echo "Testing..."
			  sh 'mvn test'
			}
		   }
		   stage('Build Docker Image') { 
			steps {
	                   script {
	                      myapp = docker.build("senthilkumar585/nginx:${env.BUILD_ID}")
	                   }
	                }
		   }
		   stage("Push Docker Image") {
	                steps {
	                   script {
			      docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
	                            myapp.push()		
	                     }
				   
	                   }
	                }
	            }
		   
	           stage('Deploy to K8s') { 
	                steps{
	                   echo "Deployment started ..."
			   sh 'ls -ltr'
			   sh 'pwd'
			   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
	                  // step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
			     step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				
			   echo "Deployment Finished ..."
	            }
	          }
	    }
	}
