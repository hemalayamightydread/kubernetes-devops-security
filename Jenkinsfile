pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
      stage('Unit Tests - JUnit and Jacoco') {
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
      stage('Docker Build and Push') {
          steps{
             withDockerRegistry([credentialsId: "docker_id", url: ""])
             {
              sh 'printenv'
              sh 'docker build -t 11261980/hemalaya:""$GIT_COMMIT"" .'
              sh 'docker push 11261980/hemalaya:""$GIT_COMMIT""'
             }
          }   
    }

      stage('Kubernetes Deployment - Dev') {
             steps  {
                   withKubeConfig([credentialsId: 'kubeconfig']) {
                  sh "sed -i 's#replace#11261980/hemalaya:""$GIT_COMMIT""#g' K8s_deployment_services.yaml"
                  sh "kubectl apply -f k8s_deployment_service.yaml"
    }
  }
}
  }
}
