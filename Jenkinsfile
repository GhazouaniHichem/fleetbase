pipeline {
    agent any

    stages {

        stage('Cleanup Workspace'){
            steps {
                script {
                    cleanWs()
                }
            }
        }

        stage('Git Checkout ') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build Images') {
            steps {
                   script {
                       
                            sh 'docker-compose build --no-cache console'
                            sh 'docker-compose build --no-cache application'
                            sh 'docker-compose build --no-cache httpd'
                   } 
            }
        }

        stage('Scan Docker Images') {
            steps {
                    sh "trivy image ghazouanihm/fleetbase_console"
                    sh "trivy image ghazouanihm/fleetbase_application"
                    sh "trivy image ghazouanihm/fleetbase_httpd"   
            }
        }

        stage('Push Images to DockerHub') {
            steps {           
                    script {
                        int x = "${BUILD_NUMBER}" as Integer; 
                        def TAG = "${x/10}";

                        withDockerRegistry([ credentialsId: 'docker-cred', url: '' ]) {
                            
                                sh "docker tag ghazouanihm/fleetbase_console ghazouanihm/fleetbase_console:${TAG}"
                                sh "docker push ghazouanihm/fleetbase_console:${TAG}"
                            
                                sh "docker tag ghazouanihm/fleetbase_application ghazouanihm/fleetbase_application:${TAG}"
                                sh "docker push ghazouanihm/fleetbase_application:${TAG}"
                            
                                sh "docker tag ghazouanihm/fleetbase_httpd ghazouanihm/fleetbase_httpd:${TAG}"
                                sh "docker push ghazouanihm/fleetbase_httpd:${TAG}"      
                        }
                   } 
            }
        }

        stage('Deploy Application') {
            steps {
                script {                       
                    sh 'docker-compose up -d'
                }
            }
        
        }
    }
}
