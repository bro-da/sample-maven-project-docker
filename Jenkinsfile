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
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
       
            stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
         stage('Docker') {
            steps {
                script {
                // sh 'docker rmi maven-sample'
                sh 'docker build -t vivans/sample-build:${TAG} .'
                
                sh 'docker images'
                }
             }
            }
        stage('Push') {
            
			steps {
//                  sh 'docker push vivans/sample-build:"${BUILD_NUMBER}"'
//                 sh 'docker tag vivans/sample-build:${BUILD.NUMBER} vivans/sample-build:latest'
				sh 'docker tag vivans/sample-build:${TAG} vivans/sample-build:latest'
				sh 'docker push vivans/sample-build:${TAG}'
				sh 'docker push vivans/sample-build:latest'
				
			}
		}
	}

	post {
		always {
			sh 'docker logout'
		}
	}
        // stage('Docker Build') {
        //     steps {
        //         script {
        //             docker.build("vivans/sample-build:${TAG}")
        //         }
        //     }
        // }

    }
    
