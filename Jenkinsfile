pipeline {
    agent any
    tools {
	    maven "MAVEN3"
	    jdk "OracleJDK8"
	}

    environment {
        registryCredential = 'ecr:us-west-2:awscreds'
        appRegistry = "992382454206.dkr.ecr.us-west-2.amazonaws.com/vprofileappimg"
        vprofileRegistry = "https://992382454206.dkr.ecr.us-west-2.amazonaws.com"
        cluster = "vprofilecluster"
        service = "vprofileappsvc"
    }
  stages {
    stage('Fetch code'){
      steps {
        git branch: 'docker', url: 'https://github.com/akshaykm111/vprofile-project.git'
      }
    }


    stage('Test'){
      steps {
        sh 'mvn test'
      }
    }


    stage('Build App Image') {
       steps {
       
         script {
                dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Docker-files/app/multistage/")
             }

     }
    
    }

    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( vprofileRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
     }

    stage('Deploy to ecs') {
         steps {
          withAWS(credentials: 'awscreds', region: 'us-west-2') {
           sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
          }
         }
    }

  }
}