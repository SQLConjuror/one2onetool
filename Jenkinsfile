pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
             steps {
                sh 'docker build . -t mahikero/cicdproject:$BUILD_NUMBER'
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withDockerRegistry([ credentialsId: "dockerHub ", url: ""]) {
                    sh 'docker push mahikero/cicdproject:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to staging') {
            when {
                branch 'staging'
            }
            steps {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.77.189.136 "docker stop web; \
                    docker rm web; \
                    docker run -p 80:3000 -v /home/ubuntu/one2onetool/:/usr/src/app --name web -d mahikero/cicdproject:$BUILD_NUMBER "'
            }
      
        }

        stage('Deploy to prod') {
            when {
                branch 'release'
            }
            steps {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.251.193.75 "docker stop web; \
                    docker rm web; \
                    docker run -p 80:3000 -v /home/ubuntu/one2onetool/:/usr/src/app --name web -d mahikero/cicdproject:$BUILD_NUMBER "'
                
            }

        }


    }
    post {
       // send email notification the specicied addresses if the build fails
       failure {  
             mail bcc: '', body: "<b>Failed Jenkins Build</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL of the build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'jenkins@build.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "mahikero@yahoo.com";  
         }
    }
}
