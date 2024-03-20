pipeline{
    agent{
        label "docker"
    }
    parameters {
        string(name: 'VERSION', defaultValue: '1.0')
    }
    environment{
        registry = '153519930658.dkr.ecr.us-east-1.amazonaws.com/traintickets'
        imagename = 'Traintickets'
    }
    stages{
        stage('Build mvn packages'){
            steps{
                sh 'mvn clean install'

            }
        }
        stage('test packages'){
            steps{
                sh 'mvn test'
            }
        }
        stage('sonar anylasis'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        sh 'mvn sonar:sonar'
                   }
                }
            }
        }
        stage('build docker image'){
            steps{
                script {

                sh "docker build -t traintickets:${params.VERSION} ."

                }
            }
        }
        stage('login docker'){
            steps{       
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 153519930658.dkr.ecr.us-east-1.amazonaws.com'
            }  
        }
        stage('tag image'){
            steps{       
            sh "docker tag traintickets:${params.VERSION} 153519930658.dkr.ecr.us-east-1.amazonaws.com/traintickets:${params.VERSION}"
            }  
        }
        stage('Push image to registry'){
            steps{       
            sh "docker push 153519930658.dkr.ecr.us-east-1.amazonaws.com/traintickets:${params.VERSION}"
            }  
        }
        stage('stop pervious container'){
            steps{       
              sh 'docker ps -f name=${imagename} -q | xargs --no-run-if-empty docker container stop'
              sh 'docker container ls -a -fname=${imagename} -q | xargs -r docker container rm'
            }  
        }
        stage('Image,run as container'){
            steps{       
            sh "docker run -itd --name ${imagename} -p 8084:8080 153519930658.dkr.ecr.us-east-1.amazonaws.com/traintickets:${params.VERSION}"
            }  
        }
    }
}
