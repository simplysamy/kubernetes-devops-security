pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //test comment
            }
        }   

      stage('test') {
            steps {
              sh "mvn test"
            }

            post {
              always {
                  junit 'target/surefire-reports/*.xml'
                  jacoco execPattern: 'target/jacoco.exec'
              }
                 
            }
        }  

      stage('Docker Build & Push') {
        steps{
          withDockerRegistry([credentialsId: "DockerHub_Creds", url:""]){
            sh 'printenv'
            sh 'docker build -t simplysamy/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push simplysamy/numeric-app:""$GIT_COMMIT""'
          }
        }
      }
  }
}
