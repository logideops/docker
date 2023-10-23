pipeline {

  environment {
    registry = "logidevops/flask"
    registry_mysql = "logidevops/mysql"
    dockerImage = ""
  }

  agent any
    stages {
  
    stage('Checkout Source') {
      steps {
        git 'https://github.com/logidevops/docker.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Flask Image') {
      steps{
        script {
          withDockerRegistry([ credentialsId: "dockerhub2", url: "" ]) {
            dockerImage.push()        
           }
        }
      }
    }

    stage('current') {
      steps{
        dir("${env.WORKSPACE}/mysql"){
          sh "pwd"
          }
      }
   }
   stage('Build mysql image') {
     steps{
        script { 
       withDockerRegistry([ credentialsId: "dockerhub2", url: "" ]) {
       sh 'docker build -t "logidevops/mysql:$BUILD_NUMBER"  "$WORKSPACE"/mysql'
       
       sh 'docker push "logidevops/mysql:$BUILD_NUMBER"'
        }
      }}}
      
    stage('Push MySQL Image') {
      steps{
        script {
          withDockerRegistry([ credentialsId: "dockerhub2", url: "" ]) {
            dockerImage.push("registry_mysql")
          }
        }
      }
    }
    //stage('Push MySQL Image') {
    //  steps{
    //    script {
    //      withDockerRegistry([ credentialsId: "dockerhub2", url: "" ]) {
    //        dockerImage.push('registry_mysql',)        
     //      }
     //   }
     // }
    // }
    
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "frontend.yaml", kubeconfigId: "kube")
        }
      }
    }

  }

}
