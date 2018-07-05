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
    stage('Package Artifacts') {
      steps {
        zip(glob: '/SampleWebApplication/bin/*.*', zipFile: ZipPackageName)
        archiveArtifacts(artifacts: ZipPackageName, onlyIfSuccessful: true, fingerprint: true)
      }
    }
    stage('Deploy') {
      when {
        branch 'master'
      }
      environment {
        AWSCredentials = 'AWSCredentials'
      }
      steps {
        withAWS(credentials: 'AWSCredentials', region: 'eu-west-1') {
          s3Upload(file: ZipPackageName, bucket: 'test.axioma.internal.depolyment', path: "${ProjectName}/${BRANCH_NAME}/${ZipPackageName}")
        }

        script {
          def files = findFiles(glob: 'SampleWebApplication/bin/*.*')
          files.each {println "RPM:  ${it}"}
        }

      }
    }
  }
  environment {
    StashedPackage = 'Package'
    ZipPackageName = 'package.zip'
    ProjectName = JOB_NAME.replace("%2F", "_")
  }
}
