pipeline {
      agent any
      stages {
          
            stage('Check in SCM') {
                  steps {
                        git 'https://github.com/exusiasoftware/customer-api.git'
                        echo 'Download Complete...'
                    
                  }
            }
          
          
            stage('Init') {
                  steps {
                        echo 'Building Sample Maven Project'
                        sh 'mvn package'
                        archiveArtifacts artifacts: '**/*.jar'
                        sh "mkdir -p /artifacts/customer-api/${env.BUILD_ID}"
                        sh "cp target/*.jar /artifacts/customer-api/${env.BUILD_ID}/"
                  }
            }
            stage('Build') {
                  steps {

                    echo 'Building Docker file'
                    sh "pwd"
                    sh "docker build . -t customerapi:${env.BUILD_ID}"
                  }
            }
            stage('Deploy') {
                  steps {
                        echo "Deploying in Staging Area"
                        sh  "docker run -d -p 80:8080 --name customer-api-build-${env.BUILD_ID}  customer-api:${env.BUILD_ID}"
                  }
            }
         
            stage('Deploy Production') {
                  steps {
                       
                       timeout(time:5, unit:'DAYS') {
                           input message: 'Approve PRODUCTION Deployment?'
                       }
                       
                      echo "Deploying in Production Area"
                      
                     sh "docker tag exusiasoftware/customer-api-build-${env.BUILD_ID}:latest"
                     sh "docker push exusiasoftware/customer-api-build-${env.BUILD_ID}:latest"
                      
                      
                      sh "docker stop customer-api-build-${env.BUILD_ID}"
                      sh "docker rm customer-api-build-${env.BUILD_ID}"
                      sh "docker rmi customer-api:${env.BUILD_ID}"
                      
                    
                  }
            }
            
            
            
      }
}
