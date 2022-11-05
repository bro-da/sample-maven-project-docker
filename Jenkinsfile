pipeline {
    agent any
    
     environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
        // DOCKERHUB_CREDENTIALS=credentials('dockerhub')
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
                 sh 'aws ecr-public get-login-password --region us-east-1|docker login --username AWS --password-stdin public.ecr.aws/l2m3f3d0'
			}
		}
        
        stage('Push') {
            
			steps {
                //  sh 'docker push vivans/sample-build:"${BUILD_NUMBER}"'
                // sh 'docker tag vivans/sample-build:${BUILD.NUMBER} vivans/sample-build:latest'
				// sh 'docker tag vivans/sample-build:${BUILD_NUMBER} vivans/sample-build:latest'
                sh 'docker tag sample-maven-project-docker:${BUILD_NUMBER} public.ecr.aws/l2m3f3d0/sample-maven-project-docker:latest'
				// sh 'docker push public.ecr.aws/l2m3f3d0/sample-maven-project-docker:${BUILD_NUMBER}'
				sh 'docker push public.ecr.aws/l2m3f3d0/sample-maven-project-docker:latest'
				
			}
		}
        stage('Helm Push ') {
                steps {
                    sh 'helm create mavenhelm'
                    sh 'pwd'
                    sh 'ls -al'
                    sh 'echo version : 0.${BUILD_NUMBER}.0 >> mavenhelm/Chart.yaml'
                    sh 'sed -i "s/tag: ""/tag: "$USER"/g" mavenhelm/values.yaml'
                    sh 'helm package mavenhelm'
                    // sh 'helm tag mavenhelm mavenhelm:${BUILD_NUMBER}'
                    sh 'aws ecr get-login-password  --region us-east-1 | helm registry login --username AWS --password-stdin 976846671615.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'helm push mavenhelm-0.${BUILD_NUMBER}.0.tgz oci://976846671615.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'rm -rf helm-maven-*'
                    }
            }
             stage('Invoke Build number to Pipeline B') {
                steps {
                    build job: 'sample-maven-project-docker-deployment', parameters : [[ $class: 'StringParameterValue', name: 'buildnum', value: "${BUILD_NUMBER}"]]
                }
            }
	}

	// post {
	// 	always {
	// 		sh 'docker logout'
	// 	}
	// }
        
    }
    
