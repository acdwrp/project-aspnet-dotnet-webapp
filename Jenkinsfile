pipeline {
  agent any
  stages {
    stage('Build1') {
      steps {
        ws(dir: 'ABS') {
          echo 'echo'
        }

        bat 'nuget restore SampleWebApplication.sln'
        bat "\"${tool 'MSBuild'}\" SampleWebApplication.sln /p:Configuration=Release /p:Platform=\"Any CPU\" /p:ProductVersion=1.0.0.${env.BUILD_NUMBER}"
      }
    }
  }
}