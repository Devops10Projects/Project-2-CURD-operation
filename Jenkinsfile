pipeline {
    agent any

        environment {
        SONAR_CLOUD = tool 'sonar-server'
    }

    stages {
        stage ("build & SonarQube analysis") {
         
            steps {
                withSonarQubeEnv('sonar-server') {
                sh ''' $SONAR_CLOUD/bin/sonar-scanner \
                        -Dsonar.organization=curdoperation \
                        -Dsonar.projectKey=curdoperation_curd \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=https://sonarcloud.io '''
              }
            }
        }
        stage ("build and push") {
 
            steps {
                script {
                    docker.withRegistry('', 'docker-login') {
                    
                    def customImage = docker.build("sairamguthula/curdoperationsaiapp:${env.BUILD_ID}")

                        /* Push the container to the custom Registry */
                    customImage.push()
                }
            }
        }
        }
        stage ("deploy to EC2") {
            steps {
                script {
                      sh 'docker rm -f $(docker ps -a) || true'
                      sh 'docker run -d -p 3000:3000 curdoperationsaiapp:${env.BUILD_ID}'
                }
              
            }
        }
    }
}
