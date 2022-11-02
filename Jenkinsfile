pipeline {
  agent any
  stages {
    stage('Verify Branch') {
      steps {
        echo "$GIT_BRANCH"
        echo "$WORKSPACE"
      }
    }

    stage('Docker Build') {
      steps {
        sh '''#!/bin/bash -e 
                  docker ps
                  docker network ls
               '''
      }
    }

    stage('Start test app') {
      post {
        success {
          echo 'App started successfully :)'
        }

        failure {
          echo 'App failed to start :('
        }

      }
      steps {
        sh '''#!/bin/bash -e 
               
               docker compose up -d
               chmod +x scripts/test_container.sh
               ./scripts/test_container.sh
            '''
      }
    }

    stage('Run Tests') {
      steps {
        sh '''#!/bin/bash -e 
               pytest ./tests/test_sample.py
            '''
      }
    }

    stage('Stop test app') {
      steps {
        sh '''#!/bin/bash -e 
               docker compose down
            '''
      }
    }

    stage('Container Scanning') {
          steps {
            script {
              // localhost.localstack.cloud:4510/voting-app
              sh '''#!/bin/bash -e 
                  echo -e "docker.io/nathanratliff/new" > anchore_images
              '''
            }
            anchore(bailOnFail: true, bailOnPluginFail: true, name: 'anchore_images', engineverify: true)
          }
    }

  }
}