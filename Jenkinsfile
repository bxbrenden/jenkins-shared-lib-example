@Library('jsle') _

pipeline {
  agent any
  stages {
    stage('Test shared lib') {
      steps {
        script {
          def output = common.useFoo()
          echo output
        }
      }
    }
  }
}
