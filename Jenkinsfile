pipeline {
  agent {
    node {
      label 'docker_in_docker'
    }
  }
  stages {
    stage('Get Oracle Docker Sources') {
      steps {
        //checkout([$class: 'GitSCM', branches: [[name: '*/master'], [name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github_id', name: 'tkleiber', url: 'https://github.com/tkleiber/com.oracle.docker.images.oracle.database.xe.git'], [credentialsId: 'github_id', name: 'oracle', url: 'https://github.com/oracle/docker-images.git']]])
        dir ('oracle') {
          git branch: 'main', credentialsId: 'github_id', url: 'https://github.com/oracle/docker-images.git'
        }
        sh 'ls -la'
      }
    }
    stage('Build Oracle Docker Images') {
      parallel {
        stage('Build Database XE 18.4.0') {
          steps {
            dir(path: 'oracle/OracleDatabase/SingleInstance/dockerfiles') {
              sh 'ls -la'
              sh 'if [ ! -f 18.4.0/oracle-database-xe-18c-1.0-1.x86_64.rpm ]; then cp /software/Oracle/Database/oracle-database-xe-18c-1.0-1.x86_64.rpm 18.4.0/oracle-database-xe-18c-1.0-1.x86_64.rpm; fi'
              sh './buildContainerImage.sh -v 18.4.0 -x'
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
