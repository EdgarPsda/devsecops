// Update Jenkinsfile
pipeline{
  agent any
  stages{
    stage('Build Artifact'){
      steps{
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }

    stage('Unit Tests - JUnit and Jacoco'){
      steps{
        sh "mvn test"
      }
      post {
        always{
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }

    stage('Mutation Tests - PIT'){
      steps{
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
      post{
        always{
          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
      }
    }

    stage('SonarQube Analysis') {
      withSonarQubeEnv() {
        sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.projectName='numeric-application'"
      }
    }

    stage('Docker Build and Push'){
      steps{
        sh 'printenv'
        sh 'docker build -t edgarpsda/numeric-app:""$GIT_COMMIT"" .'
        sh 'docker push edgarpsda/numeric-app:""$GIT_COMMIT""'
      }
    }

    stage('Kubernetes Deployment - DEV'){
      steps{
        withKubeConfig([credentialsId: 'kubeconfig']){
          sh "sed -i 's#replace#edgarpsda/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }


  }
}