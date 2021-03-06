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
            stash(name: 'code', excludes: '.git')
          }
        }

        stage('test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

    stage('push docker app') {
      agent any
      when {
        branch 'master'
      }
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
      }
    }

    stage('Component test') {
      agent any
      when {
        not {
          branch 'dev/*'
        }

      }
      steps {
        unstash 'code'
        sh 'ci/component-test.sh'
      }
    }

  }
  environment {
    docker_username = 'jonawar'
  }
}