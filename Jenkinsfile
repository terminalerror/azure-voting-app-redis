pipeline {
   agent any

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
            echo "$WORKSPACE"
         }
      }
      // cd azure-vote/
      stage('Docker Build') {
         steps {
            sh '''#!/bin/bash -e 
                  docker ps
                  docker network ps
               '''
               // source ${WORKSPACE}/
               // docker build -t jenkins-pipeline .
            // sh '''#!/bin/bash -e 
            //    docker images -a
               
            //    docker images -a
            //    cd ..
            // '''
         }
      }
      stage('Start test app') {
         steps {
            sh '''#!/bin/bash -e 
               source ${WORKSPACE}/ 
               docker-compose up -d
               ./scripts/test_container.ps1
            '''
         }
         post {
            success {
               echo "App started successfully :)"
            }
            failure {
               echo "App failed to start :("
            }
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
               docker-compose down
            '''
         }
      }
      stage('Container Scanning') {
         parallel {
            stage('Run Anchore') {
               steps {
                  sh '''#!/bin/bash -e 
                     Write-Output "blackdentech/jenkins-course" > anchore_images
                  '''
                  anchore bailOnFail: false, bailOnPluginFail: false, name: 'anchore_images'
               }
            }
            stage('Run Trivy') {
               steps {
                  sleep(time: 30, unit: 'SECONDS')
                  sh '''#!/bin/bash -e 
                  // C:\\Windows\\System32\\wsl.exe -- sudo trivy blackdentech/jenkins-course
                  '''
               }
            }
         }
      }
      stage('Deploy to QA') {
         environment {
            ENVIRONMENT = 'qa'
         }
         steps {
            echo "Deploying to ${ENVIRONMENT}"
            acsDeploy(
               azureCredentialsId: "jenkins_demo",
               configFilePaths: "**/*.yaml",
               containerService: "${ENVIRONMENT}-demo-cluster | AKS",
               resourceGroupName: "${ENVIRONMENT}-demo",
               sshCredentialsId: ""
            )
         }
      }
      stage('Approve PROD Deploy') {
         when {
            branch 'master'
         }
         options {
            timeout(time: 1, unit: 'HOURS') 
         }
         steps {
            input message: "Deploy?"
         }
         post {
            success {
               echo "Production Deploy Approved"
            }
            aborted {
               echo "Production Deploy Denied"
            }
         }
      }
      stage('Deploy to PROD') {
         when {
            branch 'master'
         }
         environment {
            ENVIRONMENT = 'prod'
         }
         steps {
            echo "Deploying to ${ENVIRONMENT}"
            acsDeploy(
               azureCredentialsId: "jenkins_demo",
               configFilePaths: "**/*.yaml",
               containerService: "${ENVIRONMENT}-demo-cluster | AKS",
               resourceGroupName: "${ENVIRONMENT}-demo",
               sshCredentialsId: ""
            )
         }
      }
   }
}
