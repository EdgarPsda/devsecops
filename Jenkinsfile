// Update Jenkinsfile
// Another update 
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

    stage('Docker Build and Push'){
      steps{
        sh 'printenv'
        sh 'docker build -t edgarpsda/numeric-app:""$GIT_COMMIT"" .'
        sh 'docker push edgarpsda/numeric-app:""$GIT_COMMIT""'
      }
    }


  }
}