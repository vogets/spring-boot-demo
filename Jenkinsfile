pipeline {
  agent any

  tools {
    jdk 'JDK8'
    maven 'maven'
  }

  stages {
    stage('Build') {
      tools {
                   jdk "JDK8"
                }
      steps {
         sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              echo "JAVA_HOME = ${JAVA_HOME}"
            '''
        sh 'java -version'
        sh 'javac -version'
        sh 'mvn -version'
        withMaven(maven : 'maven') {
          sh "mvn package"
        }
      }
    }

    

    stage('Create and push container') {
      tools {
                   jdk "JDK8"
                }
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
          withMaven(maven : 'maven') {
            sh "mvn jib:build"
          }
        }
      } 
    }


    stage('Deploy to K8s') {
      tools {
                   jdk "JDK8"
                }
      steps {
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
          sh 'chmod u+x ./kubectl'
          sh './kubectl apply -f k8s.yaml'
        }
      } 
    }
  }
}
