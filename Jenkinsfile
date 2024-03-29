pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'cd GoSecuriApp && mvn --version && mvn clean && mvn package && mvn install'
      }
    }

    stage('Run') {
      steps {
        sh 'cd GoSecuriApp && java -jar target/GoSecuriApp-1.0-SNAPSHOT.jar'
        sh 'docker ps'
        
        sh 'cp GoSecuriApp/src/main/java/com/epsi/gosecuri/generatedFiles/.htpasswd   $HOME/gosecuri/'
        sh 'cp GoSecuriApp/src/main/java/com/epsi/gosecuri/generatedFiles/*  $HOME/gosecuri/'
        sh 'cp -r GoSecuriApp/src/main/java/com/epsi/gosecuri/ressourceFiles/ $HOME/gosecuri/'
        
        //sh 'cd GoSecuriApp/src/main/java/com/epsi/gosecuri/ && ls -la'
        stash includes: 'GoSecuriApp/src/main/java/com/epsi/gosecuri/generatedFiles/*', name: 'generatedFiles'
        stash includes: 'GoSecuriApp/src/main/java/com/epsi/gosecuri/ressourceFiles/*', name: 'ressourceFiles'
      }
    }

    stage('Deploy') {
      agent {
        docker {
          image 'gosecuri:latest'
          args '-u root --privileged'
        }
      }
      steps {
        dir('html'){
          unstash 'generatedFiles'
          unstash 'ressourceFiles'
        }
        sh 'cp html/GoSecuriApp/src/main/java/com/epsi/gosecuri/generatedFiles/.htpasswd  /usr/share/nginx/html/'
        sh 'cp html/GoSecuriApp/src/main/java/com/epsi/gosecuri/generatedFiles/*  /usr/share/nginx/html/'
        sh 'cp -r html/GoSecuriApp/src/main/java/com/epsi/gosecuri/ressourceFiles/ /usr/share/nginx/html/'
      }
    }
  }
  tools {
    maven 'Maven'
    nodejs 'NodeJS'
  }
}
