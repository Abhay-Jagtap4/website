pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:abhay'
       appRegistry = "766134958101.dkr.ecr.ap-northeast-1.amazonaws.com/capstoneproject_202051204"
       capstoneRegistry = "https://766134958101.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "CapstoneProject_202051204"
        service = "capstone_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Abhay-Jagtap4/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'abhay', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
