pipeline {
    agent { label 'node115' }

    stages {
        stage('Clone Git repository') {
            steps {
                git 'https://github.com/YMazurau/Jenkins.Pipeline.git'
            }
        }

        stage('Validate Dockerfile') {
            steps {
                sh 'docker run --rm -i hadolint/hadolint < Dockerfile'
            }
        }

        stage('Build and Test Image') {
            steps {
                sh 'docker build -t ymazurau/project:itbtv.1 .'
                sh 'sleep 5' 

                timeout(time: 5, unit: 'SECONDS') {
                    retry(1) {
                        script {
                                 sh '''
                                    docker-compose up -d
                                    response=$(curl -s -o /dev/null -w "%{http_code}\n" http://localhost:3000)
                                    docker-compose down
                                    if [ "$response" != "200" ]
                                    then
                                        exit 1
                                    fi
                                    '''

                // sh 'docker stop $(docker ps -a -q -f ancestor=ymazurau/project:itbtv.1)'
                // sh 'docker rm $(docker ps -a -q -f ancestor=ymazurau/project:itbtv.1)'
             }
         }
     }
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
                sh 'kubectl apply -f preprod.yml --namespace preprod'

                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        // Test if deployment is successful
                        try {
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
                sh 'kubectl apply -f prod.yml --namespace prod'

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
                slackSend (channel: 'jenkins', color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        
            }
            failure {
                slackSend (channel: 'jenlins', color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
    }
}
