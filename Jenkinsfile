pipeline {
           
    agent {
        label "slave1"
    }
       
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/vimallinuxworld13/jenkins-docker-maven-java-webapp.git'
            }
        }
        
        
          stage("Maven Clean Package"){
               steps {
                    sh "mvn clean package"
               }
          }
               
          stage(" Build Docker Image "){
               steps {
                    sh 'sudo docker build -t 34341755/java-web-app:${BUILD_NUMBER} .'
               }
      
    }
          stage("'Push Docker Image"){
              steps {
                  withCredentials([usernamePassword(credentialsId: '8c517f95-9167-4b4a-8e55-092d79d24be5', passwordVariable: 'passwd', usernameVariable: 'dockeru')]) {
                    sh 'sudo docker login -u ${dockeru} -p ${passwd}'
                    }
                sh 'sudo docker push  34341755/java-web-app:${BUILD_NUMBER}'    
              }
          }
          
          stage("Deploy webAPP in Dev ENV"){
              steps{
                  sh 'sudo docker rm -f java-web-app'
                  sh 'sudo docker run -d -p 8080:8080 --name java-web-app 34341755/java-web-app:${BUILD_NUMBER} '
              }
          }
          
          stage("Deploy webAPP in QA ENV"){
              steps{
                  sshagent(['4a879bc0-41d5-4982-a8ed-833c16f34fd9']) {
                      
                      sh "ssh -o StrictHostKeyChecking=no ec2-user@54.237.100.168 sudo docker rm -f java-web-app"
                      sh "ssh ec2-user@54.237.100.168 sudo docker run -d -p 8080:8080 --name java-web-app 34341755/java-web-app:${BUILD_NUMBER}"
                }
              }
          }
         /* stage("QA Test"){
              steps{
                  sh "curl http://54.237.100.168:8080/java-web-app/ | grep India "
              }
          } */
          stage("Approved"){
              steps{
                  script {
            Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
            echo 'userInput: ' + userInput

            if(userInput == true) {
                // do action
            } else {
                // not do action
                echo "Action was aborted."
            }
              }
          }
          }
          stage("Deploy webAPP in Prod ENV"){
              steps{
                  sshagent(['4a879bc0-41d5-4982-a8ed-833c16f34fd9']) {
                      
                      sh "ssh -o StrictHostKeyChecking=no ec2-user@54.162.216.154 sudo docker rm -f java-web-app"
                      sh "ssh ec2-user@54.162.216.154 sudo docker run -d -p 8080:8080 --name java-web-app 34341755/java-web-app:${BUILD_NUMBER}"
                }
              }
          }

}
}
