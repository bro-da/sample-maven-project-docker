pipeline {
    agent any
    
     environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    }
    options {
        skipStagesAfterUnstable()
    }
   
    stages {
         stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/bro-da/simple-java-maven-app.git'])
 
      }
    }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
         stage('Docker build') {
            steps {
                script {
                // sh 'docker rmi maven-sample'
                sh 'docker build -t sample-maven-project-docker:${BUILD_NUMBER} .'
                
                sh 'docker images'
                }
             }
            }
        
       
            stage('Login') {

			steps {
				// sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                 sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l2m3f3d0'
			}
		}
        
        stage('Push') {
            
			steps {
//                  sh 'docker push vivans/sample-build:"${BUILD_NUMBER}"'
//                 sh 'docker tag vivans/sample-build:${BUILD.NUMBER} vivans/sample-build:latest'
				// sh 'docker tag vivans/sample-build:${BUILD_NUMBER} vivans/sample-build:latest'
                sh 'docker tag sample-maven-project-docker:${BUILD_NUMBER} public.ecr.aws/l2m3f3d0/sample-maven-project-docker:latest'
				sh 'docker push public.ecr.aws/l2m3f3d0/sample-maven-project-docker:latest'
				sh 'docker push public.ecr.aws/l2m3f3d0/sample-maven-project-docker:latest'
				
			}
		}
        stage('Helm Push ') {
                steps {
                    sh 'helm create mavenhelm'
                    // sh 'echo version : 0.${BUILD_NUMBER}.0 >> mavenhelm/Chart.yaml'
                    sh 'sed -i "s/tag:""/tag:$(BUILD_NUMBER)/g" helmmaven/values.yaml'
                    sh 'helm package mavenhelm'
                    // sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l2m3f3d0'
                    // sh 'helm push helm-maven-0.${BUILD_NUMBER}.0.tgz oci://182203249444.dkr.ecr.us-west-2.amazonaws.com/'
                    sh 'rm -rf helm-maven-*'
                    }
            }
	}

	post {
		always {
			sh 'docker logout'
		}
	}
        
    }
    
