pipeline {

	agent any 

	environment {

		PROJECT_ID = 'vasudha-devops'

		CLUSTER_NAME = 'sprint6-kube-demo'

		LOCATION = 'us-central1-c'

		CREDENTIALS_ID = 'VASUDHA-DEVOPS'

	}

	stages {

		stage ("Checkout code") {

			steps {

				checkout scm 

			}

		}

		stage ("Build") {

			steps {

				echo "cleaning and packaging"

				sh 'mvn clean package'

			}

		}

		stage ("Test") {

			steps {

				echo "Testing"

				sh 'mvn test'

			}

		}

		stage("Build image") {

            steps {

                script {

                    myapp = docker.build("narayvas/myapp4k8s:${env.BUILD_ID}")

                }

            }

        }

        stage("Push image") {

            steps {

                script {

                    docker.withRegistry('https://registry.hub.docker.com', 'e7b55f12-1b6f-42fa-b0ba-ff6142756cd2') {

                            myapp.push("${env.BUILD_ID}")

                    }

                }

            }

        }        

        stage('Deploy to Google Kubernetes') {

            steps{

			    echo "Deployment started"

				sh 'ls -ltr'

				sh 'pwd'

                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"

                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])

				echo "Deployment Finished"

            }

        }

    }    

}
