pipeline {
    agent { label 'node115' }

    stages {
        stage('Clone Git repository') {
            steps {
                git 'https://github.com/YMazurau/Jenkins.Pipeline.git'
            }
        }


        stage('Build and Test Image') {
            steps {
                sh 'sleep 5' 
                sh 'docker-compose up -d'

                timeout(time: 5, unit: 'SECONDS') {
                    retry(1) {
                        script {
                                 sh '''
                                    response=$(curl -s -o /dev/null -w "%{http_code}\n" http://localhost:3000)
                                    echo $response
                                    if [ "$response" != "200" ]
                                    then
                                        exit 1
                                    fi
                                    '''
             }
         }
     }
                sh 'docker build -t ymazurau/project:itbtv.1 .'
   }
 } 

        stage('Validate Dockerfile') {
            steps {
                sh 'docker run --rm -i hadolint/hadolint < Dockerfile'
            }
        }

        stage('Push Image to Registry') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'dockerhub-credentials',
                            usernameVariable: 'REGISTRY_USERNAME',
                            passwordVariable: 'REGISTRY_PASSWORD'
                        )
                    ]) {
                        sh 'docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD'
                        sh 'docker push ymazurau/project:itbtv.1'
                    }
                }
            }
        }

        stage('Deploy to Pre-Prod') {
            steps {
                input message: 'Approve Deployment to Pre-Prod', submitter: 'jenkins'

                // Deploy to Pre-Prod namespace
                // sh 'kubectl config use-context your-kubectl-context'
                // sh 'ssh -L 6443:127.0.0.1:6443 ym@192.168.56.116 -f -N'
                sh 'kubectl create namespace preprod'
                sh 'kubectl apply -f dbpass_secret.yaml'
                sh 'kubectl apply -f preprod.yaml'

                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        // Test if deployment is successful
                        try {
                            sh 'sleep 15'
                            sh 'kubectl rollout status deployment/project --namespace preprod'
                        } catch (Exception e) {
                            error "Deployment to Pre-Prod failed"
                        }

                        // Display message and prompt for approval
                        echo 'Deployment to Pre-Prod is successful'
                        input message: 'Approve Deployment to Prod', submitter: 'jenkins'
                    }
                }
            }
        }

        stage('Cleaning') {
            steps {
                // Remove deployment from Pre-Prod namespace
                sh 'kubectl delete deployment project --namespace preprod'
            }
        }
        stage('Deploy to Prod') {
            steps {
                // Deploy to Prod namespace
                sh 'kubectl create namespace prod'
                sh 'kubectl apply -f Proddbpass_secret.yaml'
                sh 'kubectl apply -f prod.yaml'

                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        // Test if deployment is successful
                        try {
                            sh 'kubectl rollout status deployment/project --namespace prod'
                        } catch (Exception e) {
                            error "Deployment to Prod failed"
                        }
                    }
                }
            }
        }

    }

    post {
            success {
                slackSend (channel: 'incoming-webhooks', color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                sh 'docker-compose down'
        }
            failure {
                slackSend (channel: 'incoming-webhooks', color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                sh 'docker-compose down'
            }
    }
}
