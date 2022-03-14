pipeline {
  agent any
  
  environment {
    LOGIN_URL = 'https://c108-e.eu-gb.containers.cloud.ibm.com'
    LOGIN_PORT = '32125'
    PROJECT = 'pmg-carrental-jenkins'
  }  
  stages {
    stage ('Initialize') {
      steps {
        sh '''
          echo "PATH = ${PATH}"
          echo "M2_HOME = ${M2_HOME}"
        ''' 
      }
    }
   
    stage('Preamble') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("${env.PROJECT}") {
              echo "Using project: ${openshift.project()}"
            }
          }
        }
      }
    }  
   
    stage('Delete') {
      steps {
        echo "Delete application"
        script {
          openshift.withCluster() {
            openshift.withProject("${env.PROJECT}") {
              openshift.selector("all", [  'app' : 'openshift-python' ]).delete()
            }
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy application'
        script {
          sh 'oc new-app https://github.com/chongdershubhayu/openshift-python-sample.git#main --strategy=docker --context-dir=/src'
        }

      }
    }
    stage('Expose') {
      steps {
        echo 'Expose Route'
        script {
          sh 'oc expose svc/openshift-python-sample'
        }

      }
    }
  }
}
