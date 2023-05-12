pipeline {
  agent {
    dockerfile {
      filename: "Dockerfile.uwsgi"
      additionalBuildArgs: "-t ${params.IMAGE_NAME}"
      args: "--name ${params.IMAGE_NAME}-cont"
    }
  }
  parameters {
    string(name: 'IMAGE_NAME', defaultValue: 'newDockerImg', description: 'Name that will have Docker Image')
  }
  stages {
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
        echo "docker save ${params.IMAGE_NAME} | gzip > ${params.IMAGE_NAME}.tar.gz"
      }
    }
    stage('Load image') {
      steps {
        echo "docker load < ${params.IMAGE_NAME}.tar.gz"
      }
    }
    stage('Run container') {
      steps {
        echo "docker run -d -p 8090:5000 --name ${params.IMAGE_NAME}-c ${params.IMAGE_NAME}"
      }
    }
    stage('Test') {
      steps {
        echo "curl -v http://localhost:8090/ai-quotes | grep 200"
      }
      post {
        success {
          archiveArtifact ${params.IMAGE_NAME}.tar.gz
        }
      }
    }
  }
}
