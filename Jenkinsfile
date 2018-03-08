pipeline {
   agent any

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
