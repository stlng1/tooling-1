pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }

  stages {
    stage('Build image for tooling-app') {
      steps {
        sh 'docker build -t stlng/tooling-master:0.0.2 .'
      }
    }

    stage('Test Stage: testing endpoint') {
      steps {
        sh 'docker-compose -f tooling.yml  up -d'
      }
      }
      
    stage('Test Stage: testing endpoint2') {
        steps {
        script {
          while (true) {
            def response = httpRequest 'http://localhost:5000'
            if (response.status == 200) {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                }
                break 
                }
              }
            }
          }

    stage('Push docker image to docker hub registry') {
      when { getRC.equals(200) }
      steps {
        sh 'docker push stlng/tooling-master:0.0.2'
      }
    }

    stage('Cleaning up') {
      steps{
        sh 'docker compose -f tooling.yml down'
        sh 'docker rmi tooling-master:0.0.2'
      }
    } 
   }
  }
