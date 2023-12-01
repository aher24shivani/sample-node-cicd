pipeline {
   agent any
     options {
     buildDiscarder(logRotator(numToKeepStr: '5'))
     }
     environment {
     DOCKERHUB_CREDENTIALS = credentials('dockerhub')
     SSH_CREDENTIALS = credentials('ssh_into_ec2')
     EC2_INSTANCE_IP = '174.129.65.106'
     }
      stages {
         stage('Checkout') {
             steps {
                 script {
                     // Checkout code from the 'main' branch
                     checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url:'https://github.com/aher24shivani/sample-node-cicd.git']]])
                 }
             }
         }

         stage('Install Dependencies') {
             steps {
                 script {
                     // Install Node.js dependencies
                     sh 'npm install --save-dev mocha chai'
                 }
             }
         }

         stage('Build') {
             steps {
                 script {
                     // Build the application using docker image
                     sh 'docker build . -t ahershiv/to-do-node-app'
                 }
             }
         }

        
         stage('Push to registry') {
       steps {
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
          sh 'docker push ahershiv/to-do-node-app'
         }
        }

         stage('Run Tests') {
             steps {
                 script {
                    // Run Mocha tests and capture the exit code
            def testsExitCode = sh(script: 'npm test', returnStatus: true)

            // Check the exit code and act accordingly
            if (testsExitCode != 0) {
                error("Tests failed. Exiting the pipeline.")
            } else {
                echo "Tests passed. Proceeding with the pipeline."
                 }
               }//script
             } //steps
         }
        
         stage('Cleanup Docker Resources') {
            steps {
                script {
                    sh 'docker system prune -f'
                }
            }
        }

         stage('Deploy to Stage') 
          {
            steps 
              {
                script 
                  {
                   // Stage environment set on ec2 instance
                   withCredentials([sshUserPrivateKey(credentialsId: 'ssh_into_ec2', keyFileVariable: 'SSH_KEY')]) {
                     script {
                     def remoteScript = """
                     docker pull ahershiv/to-do-node-app
                     docker stop to-do-node-app || true
                     docker rm to-do-node-app || true
                     docker run -d -p 8000:8000 --name to-do-node-app ahershiv/to-do-node-app
                     """
                     sh "echo '''${remoteScript}''' > remote_script.sh"
                     sh "cat remote_script.sh | ssh -o StrictHostKeyChecking=no -i \$SSH_KEY ec2-user@\$EC2_INSTANCE_IP /bin/bash -s"
                     }
                   }
                 } //script
                } //steps
          } //stage
          
     }  //stages

     post {
       always {
             // Cleanup or additional steps that should be executed regardless of the build result
                cleanWs(cleanWhenNotBuilt: false,
                     deleteDirs: true,
                     disableDeferredWipeout: true,
                     notFailBuild: true,
                     patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                                [pattern: '.propsfile', type: 'EXCLUDE']])
              }

/*        success {
            emailext subject: "Pipeline Successful - ${currentBuild.fullDisplayName}",
                      body: "The pipeline for ${env.JOB_NAME} ${env.BUILD_NUMBER} has successfully completed.",
                      to: 'shivaanii.aher@gmail.com'
        }
        failure {
            emailext subject: "Pipeline Failed - ${currentBuild.fullDisplayName}",
                      body: "The pipeline for ${env.JOB_NAME} ${env.BUILD_NUMBER} has failed.\n\nConsole Output:\n${Jenkins.instance.getItem(env.JOB_NAME).getBuildByNumber(env.BUILD_NUMBER).getLog(100)}",
                      to: 'shivaanii.aher@gmail.com'
        }   */
    }

 } //pipeline
