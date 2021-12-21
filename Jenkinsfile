pipeline {
  agent {
    node {
      label 'docker_in_docker'
    }
  }
  stages {
    stage('Get Oracle Docker Sources') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: 'origin/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CloneOption', depth: 0, noTags: true, reference: '', shallow: false, timeout: 30]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github_id', url: 'https://github.com/oracle/docker-images.git']]])
      }
    }
    stage('Build Oracle Docker Images') {
      parallel {
        stage('Build Database XE 18.4.0') {
          steps {
            dir(path: 'OracleDatabase/SingleInstance/dockerfiles') {
              sh 'ls -la'
              sh 'if [ ! -f 18.4.0/oracle-database-xe-18c-1.0-1.x86_64.rpm ]; then cp /software/Oracle/Database/oracle-database-xe-18c-1.0-1.x86_64.rpm 18.4.0/oracle-database-xe-18c-1.0-1.x86_64.rpm; fi'
              sh 'sudo ./buildDockerImage.sh -v 18.4.0 -x'
              sh 'docker tag oracle/database:18.4.0-xe localhost:5000/oracle/database:18.4.0-xe'
              sh 'docker push localhost:5000/oracle/database:18.4.0-xe'
            }
          }
        }
      }
    }
    stage('Cleanup') {
      parallel {
        stage ('CleanUp Database XE 18.4.0') {
          steps {
            sh 'docker rmi --force localhost:5000/oracle/database:18.4.0-xe'
            sh 'docker rmi --force oracle/database:18.4.0-xe'
          }
        }
      }
    }
  }
}
