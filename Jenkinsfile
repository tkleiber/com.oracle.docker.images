pipeline {
  agent {
    node {
      label 'localhost_vagrant'
    }

  }
  stages {
    stage('Get Oracle Docker Sources') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: 'origin/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CloneOption', depth: 0, noTags: true, reference: '', shallow: false, timeout: 30]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'e7677693-d5e6-463f-b6d4-597e0fb91c3f', url: 'https://github.com/oracle/docker-images.git']]])
      }
    }
    stage('Build Oracle Docker Images') {
      steps {
        parallel(
          "Build Database XE 11.2.0.2": {
            dir(path: 'OracleDatabase/dockerfiles') {
              sh 'if [ ! -f 11.2.0.2/oracle-xe-11.2.0-1.0.x86_64.rpm.zip ]; then cp /software/Oracle/Database/oracle-xe-11.2.0-1.0.x86_64.rpm.zip 11.2.0.2/oracle-xe-11.2.0-1.0.x86_64.rpm.zip; fi'
              sh 'sudo ./buildDockerImage.sh -v 11.2.0.2 -x'
              sh 'docker tag oracle/database:11.2.0.2-xe localhost:5000/oracle/database:11.2.0.2-xe'
              sh 'docker push localhost:5000/oracle/database:11.2.0.2-xe'
            }
          }
        )
      }
    }
    stage('Cleanup') {
      steps {
        parallel(
          "CleanUp Database XE 11.2.0.2": {
            sh 'docker rmi --force localhost:5000/oracle/database:11.2.0.2-xe'
            sh 'docker rmi --force oracle/database:11.2.0.2-xe'
          }
        )
      }
    }

  }
}
