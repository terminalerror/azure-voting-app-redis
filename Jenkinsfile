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

      stage('Container Scanning') {
         // parallel {
         stage('Run Anchore') {
            steps {
               script {
                  echo -e "hello-world" > anchore_images
               }
               
               // sh '''#!/bin/bash -e 
               //    echo -e "hello-world" > anchore_images
               // '''
               // nathanratliff/jenkins-build-agent:2.2\nnathanratliff/docker-dind:1.0
               anchore(bailOnFail: true, bailOnPluginFail: true, name: 'anchore_images', engineverify: true, forceAnalyze: true)
            }
         }
            // stage('Run Trivy') {
            //    steps {
            //       sleep(time: 1, unit: 'SECONDS')
            //       // sh '''#!/bin/bash -e  \nnathanratliff/keycloak:latest
            //       // // C:\\Windows\\System32\\wsl.exe -- sudo trivy blackdentech/jenkins-course
            //       // '''
            //    }
            // }
         // }
      }
    }

  }
}