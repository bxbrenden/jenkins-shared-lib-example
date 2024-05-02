@Library('jsle') _

pipeline {
  agent any
  stages {
    stage('Test shared lib') {
      steps {
        script {
          def goodbye = common.useBar()
          echo goodbye
        }
      }
    }
  }
}
