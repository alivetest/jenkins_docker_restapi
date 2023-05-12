pipeline {
  agent any
  // agent {
  //   dockerfile {
  //     filename "Dockerfile.uwsgi"
  //     additionalBuildArgs "-t ${params.IMAGE_NAME}"
  //     args "--name ${params.IMAGE_NAME}-cont"
  //   }
  // }
  parameters {
    // docker image name must be lowercase
    string(name: 'IMAGE_NAME', defaultValue: 'new-restapi-img', description: 'Name that will have Docker Image')
  }
  stages {
    stage('Build') {
      agent {
        dockerfile {
          filename "Dockerfile.uwsgi"
          additionalBuildArgs "-t ${params.IMAGE_NAME} --no-cache"
          args "--name ${params.IMAGE_NAME}-cont -p 8090:5000"
        }
      }
      steps {
        sh 'ls /usr/local/bin | grep uwsgi'
      }
    }
    stage('Save Image') {
      input {
        message "Should we continue?"
        ok "Yes, we should."
        // username who can submit this input
        submitter "jack,bob,alice"
//        parameters {
//          string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
//        }
      }
      steps {
        sh "docker save ${params.IMAGE_NAME} | gzip > ${params.IMAGE_NAME}.tar.gz"
      }
    }
    stage('Load image') {
      steps {
        sh "docker load < ${params.IMAGE_NAME}.tar.gz"
      }
    }
    stage('Run container') {
      steps {
        sh "docker run -d -p 8090:5000 --name ${params.IMAGE_NAME}-c ${params.IMAGE_NAME}"
      }
    }
    stage('Test') {
      steps {
        sh "curl -is -u admin:admin http://localhost:8090/ai-quotes | grep 200"
      }
      post {
        success {
          archiveArtifacts "${params.IMAGE_NAME}.tar.gz"
          // echo 'All gone OK!'
        }
        always {
          sh "docker stop ${params.IMAGE_NAME}-c && docker rm ${params.IMAGE_NAME}-c"
        }
      }
    }
  }
}
