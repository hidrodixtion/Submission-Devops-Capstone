pipeline {
	agent any
	stages {

		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
			}
		}
		
		stage('Build Docker Image') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerHubCred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]){
					sh '''
						docker build -t hidrodixtion/capstone .
					'''
				}
			}
		}

		stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerHubCred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]){
					sh '''
						docker login -u $USERNAME -p $PASSWORD
						docker push hidrodixtion/capstone
					'''
				}
			}
		}

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'AWSCapstoneCred') {
					sh '''
						kubectl config use-context hidrodixtion@my-cluster.ap-southeast-1.eksctl.io
					'''
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'AWSCapstoneCred') {
					sh '''
						kubectl apply -f ./controller_blue.json
					'''
				}
			}
		}

		stage('Deploy green container') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'AWSCapstoneCred') {
					sh '''
						kubectl apply -f ./controller_green.json
					'''
				}
			}
		}

		stage('Create the load balancer service in the cluster, redirect to blue') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'AWSCapstoneCred') {
					sh '''
						kubectl apply -f ./lb_service_blue.json
					'''
				}
			}
		}

		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }

		stage('Create the load balancer service in the cluster, redirect to green') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'AWSCapstoneCred') {
					sh '''
						kubectl apply -f ./lb_service_green.json
					'''
				}
			}
		}

	}
}
