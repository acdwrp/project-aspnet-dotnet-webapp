pipeline {
  environment {
        StashedPackage = 'Package'
        ZipPackageName = 'package.zip'
      }
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
    stage('Package Artifacts'){
      environment {
        
      }
      steps{
        zip(glob: "${workspace}/SampleWebApplication/bin/*.*", zipFile: ZipPackageName)
        
        echo '${workspace}/'ZipPackageName
        
        stash includes: '${workspace}/'ZipPackageName, name: StashedPackage
      }
    }
    stage('Deploy') {
      environment {
        AWSCredentials = 'AWSCredentials'
      }
      steps {
        echo 'Creating package....'
        
        echo 'Uploading package to S3....'
        withAWS(credentials: 'AWSCredentials', region: 'eu-west-1') {
          s3Upload(file: ZipPackageName, bucket: 'test.axioma.internal.depolyment')
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
