pipeline      {
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
                sh 'docker build -t 'stlng/tooling-$env.BRANCH_NAME:$env.BUILD_NUMBER' .'
            }
        }

        stage('Launch app') {
            steps {
                sh 'docker compose -f tooling.yml up -d'
            }
        }

        stage('testing endpoint') {
            steps {
                httpRequest url:'http://localhost:5000',
                validResponseCodes:'200'
            }
        }

        stage('shutting down app') {
            steps{
                sh 'docker compose -f tooling.yml down'
            }
        }
    }

        stage('login to dockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('pushing image to dockerHub') {
            steps {
                sh 'docker push 'stlng/tooling-$env.BRANCH_NAME:$env.BUILD_NUMBER''
            }
        }

        stage('logout of dockerHub') {
            steps {
               sh 'docker logout'
            }
        }

 /*** workspace clean up*/
    post {
        always     {
                sh 'docker system prune'
                cleanWs()
        }
    }
}
