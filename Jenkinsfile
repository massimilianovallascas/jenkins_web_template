@Library('jenkins_shared') _

pipeline {
  agent any
  
  options {
    timestamps()
    disableConcurrentBuilds()
  }

  environment {
    GITHUB = credentials("gitcredentials")
  }
  
//   parameters {
//     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
//     text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
//     booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
//     choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
//     password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
//   }
  
  stages {
    stage("Build") {
      steps {
        echo "Building"
        helloVariable("Massi")
        script {
          utils.replaceString()
          sh """
            mkdir -p docker
          """
        }
      }
    }

    stage("Docker build") {
      agent {
        docker { 
           image "python:latest"
           args  "-v ${WORKSPACE}/docker:/home/python"
         }
      }
          steps {
            sh """
              python --version > /home/python/docker_python_version
            """
          }
        }

    stage("Test") {
      steps {
        echo "Testing"
        sh """
          chmod +x test.sh
          ./test.sh ${env.BUILD_NUMBER}
        """
      }
    }

    stage("Deploy") {
      steps {
        echo "Deploying"
        sshPublisher(
          publishers: [
            sshPublisherDesc(
              configName: 'http', 
              transfers: [
                sshTransfer(
                  cleanRemote: false, excludes: '', 
                  execCommand: 'mv index.html /var/www/html/index.html', 
                  execTimeout: 120000, 
                  flatten: false, 
                  makeEmptyDirs: false, 
                  noDefaultExcludes: false, 
                  patternSeparator: '[, ]+', 
                  remoteDirectory: '', 
                  remoteDirectorySDF: false, 
                  removePrefix: '', 
                  sourceFiles: 'index.html'
                )
              ], 
              usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false
            )
          ]
        )
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'index.html', fingerprint: true, followSymlinks: false
    }
    cleanup {
      cleanWs()
    }
  }
}
