pipeline {
	agent any
	stages {

		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
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
