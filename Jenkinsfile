pipeline {

  agent {
    kubernetes {
      yamlFile 'kaniko-builder.yaml'
    }
  }

  environment {
    APP_NAME = "example-pipeline-kaniko"
    RELEASE = "1.0.0"
    IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
  }

  stages {
    stage("Cleanup Workspace") {
      steps {
        cleanWs()
      }
    }

    stage("Checkout from SCM"){
      steps {
        git branch: 'main', url: 'https://github.com/falterfriday/example_pipeline_kaniko.git'
      }
    }

    stage('Build & Push with Kaniko') {
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''#!/busybox/sh
              /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG} --destination=${DOCKER_USER}/${APP_NAME}:latest
            '''
          }
        }
      }
    }
  }
}
