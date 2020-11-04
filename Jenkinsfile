pipeline {

  agent none


  stages{
      stage('Vote Build'){
          agent{
            docker{
              image 'python:2.7.16-slim'
              args '--user root'
            }
          }
          when{
            changeset "**/vote/**"
          }
          steps{
            echo 'Compiling vote app'
            dir('vote'){
              sh 'pip install -r requirements.txt'
            }
          }

      }
      stage('Vote UT'){
          agent{
            docker{
              image 'python:2.7.16-slim'
              args '--user root'
            }
          }
          when{
            changeset "**/vote/**"
          }
          steps{
            echo 'Running Unit Tests on vote app'
            dir('vote'){
              sh 'pip install -r requirements.txt'
              sh 'nosetests -v'
            }
          }
      }
      stage('Vote Docker BnP'){
          agent any
          when{
            changeset "**/vote/**"
            branch "master"
          }
          steps{
            echo 'Packaging vote app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def voteImage = docker.build("xxxxxx/vote:v-adb-${env.BUILD_ID}", "./vote")
                  voteImage.push()
                  voteImage.push("latest")
              }
            }
          }
      }
      stage('Deploy to Dev'){
          agent any
          when{
            branch "master"
          }
          steps{
            echo 'Deploy to Dev with Docker Compose'
              sh 'docker-compose up -d '
          }
      }
  }

  post{
    always{
        echo 'Pipeline for vote is complete..'
    }
 
  }

}

