@Library('jsle') _

pipeline {
  agent any
  stages {
    stage('Test shared lib') {
      steps {
        script {
          def hello = common.useFoo()
          echo hello
          def goodbye = common.useBar()
          echo goodbye
        }
      }
    }
  }
}
