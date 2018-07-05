pipeline {
  agent {
    node {
      label 'win'&&'slave'
      customWorkspace "workspace\\${JOB_NAME.replace("%2F", "_")}"
    }

  }
  stages {
    stage('Build') {
      steps {
        bat 'nuget restore SampleWebApplication.sln'
        bat "\"${tool 'MSBuild'}\" SampleWebApplication.sln /p:Configuration=Release /p:Platform=\"Any CPU\" /p:ProductVersion=1.0.0.${env.BUILD_NUMBER}"
        ws(dir: 'ABS') {
          echo 'echo'
        }

      }
    }
    stage('Test') {
      steps {
        echo 'Testing..'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Creating package....'
        zip(dir: "${workspace}/SampleWebApplication/bin/*.*", zipFile: 't.zip')
        echo 'Uploading package to S3....'
        withAWS(credentials: 'AWSCredentials', region: 'eu-west-1') {
          s3Upload(file: 't.zip', bucket: 'test.axioma.internal.depolyment')
        }

        script {
          def files = findFiles(glob: 'SampleWebApplication/bin/*.*')
          files.each {println "RPM:  ${it}"}
        }

        archiveArtifacts(artifacts: 'SampleWebApplication/*.*,SampleWebApplication/bin/*.*', onlyIfSuccessful: true, fingerprint: true)
      }
    }
  }
}
