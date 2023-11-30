pipeline {
  agent any
    options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    SSH_CREDENTIALS = credentials('ssh_into_ec2')
    EC2_INSTANCE_IP = '3.89.249.85'
    }
     stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout code from the 'main' branch
                    checkout([$class: 'GitSCM', branches: [[name: 'test']], userRemoteConfigs: [[url: 'https://github.com/aher24shivani/sample-node-cicd.git']]])
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
                    // Run Mocha tests
                    sh 'npm test'
                }
            }
        }    

pipeline {
  agent any
    options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    SSH_CREDENTIALS = credentials('ssh_into_ec2')
    EC2_INSTANCE_IP = '3.89.249.85'
    }
     stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout code from the 'main' branch
                    checkout([$class: 'GitSCM', branches: [[name: 'test']], userRemoteConfigs: [[url: 'https://github.com/aher24shivani/sample-node-cicd.git']]])
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
                    // Run Mocha tests
                    sh 'npm test'
                }
            }
        }    


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
         }//post


} //pipeline
