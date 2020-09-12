pipeline {
	agent any
	stages {

		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
			}
		}

        stage('Test upload S3') {
            steps {
                withAWS(region:'ap-southeast-1', credentials:'awscred') {
					s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'capstone-pipeline-bucket')
				}
            }
        }

        stage('Test upload S3') {
            steps {
                withAWS(region:'ap-southeast-1', credentials:'awsuserpass') {
					s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'capstone-pipeline-bucket')
				}
            }
        }

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'userpass') {
                    sh 'aws eks --region ap-southeast-1 update-kubeconfig --name my-cluster'
					sh 'kubectl config use-context arn:aws:eks:ap-southeast-1:365011820155:cluster/my-cluster'
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'awscred') {
					sh '''
						kubectl apply -f ./controller_blue.json
					'''
				}
			}
		}

		stage('Deploy green container') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'awscred') {
					sh '''
						kubectl apply -f ./controller_green.json
					'''
				}
			}
		}

		stage('Create the load balancer service in the cluster, redirect to blue') {
			steps {
				withAWS(region:'ap-southeast-1', credentials:'awscred') {
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
				withAWS(region:'ap-southeast-1', credentials:'awscred') {
					sh '''
						kubectl apply -f ./lb_service_green.json
					'''
				}
			}
		}

	}
}
