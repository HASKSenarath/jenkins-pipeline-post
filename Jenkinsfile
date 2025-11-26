pipeline {
    agent any
   
    stages {
        stage('Create  directory for the WEB Application') {
            steps {
                // First, drop the directory if it exists (inside Jenkins container)
                sh 'rm -rf /home/jenkins/tomcat-web'
                // Create the directory
                sh 'mkdir -p /home/jenkins/tomcat-web'
            }
        }

        stage('Drop the Apache Tomcat Docker container') {
            steps {
                echo 'dropping the container...'
                // Do not fail if the container does not exist
                sh 'docker rm -f tomcat1 || true'
            }
        }

        stage('Create the Tomcat container') {
            steps {
                echo 'Creating the container...'
                // Run Tomcat on the same "jenkins" network (for consistency) and expose it on host port 9090
                // NOTE: no volume mount here; we will copy files directly into the container
                sh 'docker run -dit --name tomcat1 --network jenkins -p 9090:8080 tomcat:9.0'
            }
        }

        stage('Copy the web application to the container directory') {
            steps {
                echo 'Copying web application into Tomcat container...'
                // Copy the "shopping" web app from the Jenkins workspace directly into Tomcat's webapps dir
                // The "shopping" folder comes from the Git repo
                sh 'docker cp shopping/. tomcat1:/usr/local/tomcat/webapps/shopping'
            }
        }
    }

    post {
        always {
            echo 'These steps are always executed'   
            cleanWs()         
        }
        success {
            // Runs only if the whole pipeline succeeds
            echo 'the deployment has worked'
        }
        failure {
            // Runs only if the pipeline fails
            echo 'An error has ocurred'
        }
    }
}
