pipeline {
     agent any
     stages {
         stage('Clone Code ') {
             steps {
                 // Build steps go here
                 git url: 'https://github.com/estebanmorenoit/django-notes-app.git', branch: 'main'
             }
         }
         stage("Build") {
             steps {
                 sh 'docker build . -t django-notes-app'
             }
         }
         stage("Push to Docker Hub") {
             steps {
                 withCredentials([usernamePassword(credentialsId: "docker", passwordVariable: "dockerhubPass", usernameVariable: "dockerhubUser")]) {
                     sh "docker tag django-notes-app ${env.dockerhubUser}/django-notes-app:latest"
                     sh "docker login -u ${env.dockerhubUser} -p ${env.dockerhubPass}"
                     sh "docker push ${env.dockerhubUser}/django-notes-app:latest"
                 }
             }
         }
         stage("Cleanup") {
             steps {
                 // Stop and remove the container if it exists
                 sh 'docker stop django-notes-app || true'
                 sh 'docker rm django-notes-app || true'
             }
         }
         stage("Deploy") {
             steps {
                 // Run the container and bind port 8000
                 sh 'docker run -d -p 8000:8000 --name django-notes-app django-notes-app'
             }
         }
     }
     post {
         success {
             emailext (
                 to: 'k_ashok766@yahoo.com',
                 subject: 'Deployment Successful',
                 body: 'The deployment to production was successful. You can access the application at http://<ec2-instance-public-ip-address>:8000',
             )
         }
         failure {
             emailext (
                 to: 'k_ashok766@yahoo.com',
                 subject: 'Deployment Failed',
                 body: 'The deployment to production failed. Please check the Jenkins console output for more details.',
             )
         }
     }
 }
