pipeline {
  agent any

  stages {

    stage('Build Artifact - Maven') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }

    stage('Unit Tests - JUnit and JaCoCo') {
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

    stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
    }

  
    // stage('SonarQube SAST') {
    //   steps {
    //     sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-lossemi.eastus.cloudapp.azure.com:9000 -Dsonar.login=02b02086a4bc71fbd5bb52217a241a09de50f66c"
    //   }
    //   post {
    //     always {
    //       pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
    //     }
    //   }
    // }

    stage('SonarQube - SAST') {
      steps {
        sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-lossemi.eastus.cloudapp.azure.com:9000 -Dsonar.login=02b02086a4bc71fbd5bb52217a241a09de50f66c"
      }
    }

    stage('Docker Build and Push') {
     steps {
       withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
         sh 'printenv'
          sh 'docker build -t mlysenko/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push mlysenko/numeric-app:""$GIT_COMMIT""'
        }
     }
    }

    //stage('Kubernetes Deployment - DEV') {
      //steps {
        //withKubeConfig([credentialsId: 'kubeconfig']) {
        //  sh "sed -i 's#replace#siddharth67/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
         // sh "kubectl apply -f k8s_deployment_service.yaml"
        //}
      //}
    //}

  }

}