pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'bat "\\"${tool \'MSBuild\'}\\" SampleWebApplication.sln /p:Configuration=Release /p:Platform=\\"Any CPU\\" /p:ProductVersion=1.0.0.${env.BUILD_NUMBER}"'
      }
    }
  }
}