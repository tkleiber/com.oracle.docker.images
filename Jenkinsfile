pipeline {
  agent {
    node {
      label 'docker_in_docker'
    }
  }
  parameters {
    choice choices: ['11.2.0.2', '18.4.0', '21.3.0'], description: 'XE Version', name: 'XE_VERSION'
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
            dir(path: 'OracleDatabase/SingleInstance/dockerfiles') {
              sh 'if [ ! -f "${XE_VERSION}"/oracle-xe-11.2.0-1.0.x86_64.rpm.zip ]; then cp /software/Oracle/Database/oracle-xe-11.2.0-1.0.x86_64.rpm.zip "${XE_VERSION}"/oracle-xe-11.2.0-1.0.x86_64.rpm.zip; fi'
              sh 'sudo ./buildDockerImage.sh -v "${XE_VERSION}" -x'
              sh 'docker tag oracle/database:"${XE_VERSION}"-xe localhost:5000/oracle/database:"${XE_VERSION}"-xe'
              sh 'docker push localhost:5000/oracle/database:"${XE_VERSION}"-xe'
            }
          }
        )
      }
    }
    stage('Cleanup') {
      steps {
        parallel(
          "CleanUp Database XE 11.2.0.2": {
            sh 'docker rmi --force localhost:5000/oracle/database:"${XE_VERSION}"-xe'
            sh 'docker rmi --force oracle/database:"${XE_VERSION}"-xe'
          }
        )
      }
    }

  }
}
