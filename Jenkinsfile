pipeline {
   agent any

     options {
     buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
     }
     
   stages {
    stage('build') {
      steps {
         sh 'ant -f build.xml -v'
     }
    }
   }
  post {
    always {
    archiveArtifactes artifactes: 'dist/*.jar', fingerprint: true
    }
  }
}
