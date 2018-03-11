pipeline {
   agent none

  environment {
    MAJOR_VERSION = 1
  }
   stages {
     stage('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
       sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
     }

    stage('build') {
         agent {
           label 'apache'
         }
      steps {
         sh 'ant -f build.xml -v'
     }
     post {
       always {
        archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
       }
     }
    }
    stage('deploy') {
        agent {
         label 'apache'
        }
    steps {
      sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
      sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
    }
  }
    stage ("Rectangle on Centos") {
     agent {
      label 'Centos'
     }
     steps {
      sh "wget http://192.168.56.106/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
     }
    }
    stage("Test on Debian") {
      agent {
        docker 'openjdk:8u151-jre'
        }
        steps {
        sh "wget http://192.168.56.106/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
        }
    }
    stage('Promote to Green') {
    agent {
       label 'apache'
     }
     when {
      branch 'master'
     }
      steps {
      sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
    stage('Promote Development Branch to Master') {
       agent {
         label 'apache'
       }
     when {
      branch 'development'
     }
     steps {
       echo "Stashing Any Local Changes"
        sh 'git stash'
        echo "Checking Out Development Branch"
        sh 'git checkout development'
        echo 'Checking Out Master Branch'
        sh 'git pull'
        sh 'git checkout master'
        echo 'Merging Development into Mater Branch'
        sh 'git merge development'
        echo 'Pushing to Origin Master'
        sh 'git push origin master'
        echo 'Tagging the Release'
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar "
     }
    }
  }
}

