pipeline {
  agent any
  stages {
    stage('Clone Down') {
      steps {
        sh 'echo "yellow ornage"'
        stash(name: 'code', excludes: '.git')
      }
    }

    stage('Parallel Execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            skipDefaultCheckout true
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

  }
}