pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //test comment
            }
      }   

      stage('Unit Test') {
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

      stage('Mutation Test - PIT') {
        steps {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
        }

        post {
          always {
              pitmutation mutationStatsFile: '**/target/pit-reports/**/mutation.xml'
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

      stage('Kubernetes deployment - DEV') {
        steps{
           withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "sed -i 's#replace#simplysamy/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
           }
        }
      }
  }
}
