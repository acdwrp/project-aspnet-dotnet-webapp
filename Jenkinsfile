pipeline {
  agent {
    node {
      label 'win'&&'slave'
      customWorkspace "workspace\\${env.ProjectName}"
    }

  }
  stages {
    stage('Build') {
      steps {
        bat 'nuget restore SampleWebApplication.sln'
        bat "\"${tool 'MSBuild'}\" SampleWebApplication.sln /p:Configuration=Release /p:Platform=\"Any CPU\" /p:ProductVersion=1.0.0.${env.BUILD_NUMBER}"
      }
    }
    stage('Test') {
      steps {
        echo 'Testing..'
      }
    }
    stage('Package Artifacts') {
      steps {
        zip(glob: 'SampleWebApplication/bin/*.*', zipFile: ZipPackageName)
        archiveArtifacts(artifacts: ZipPackageName, onlyIfSuccessful: true, fingerprint: true)
      }
    }
    stage('Push to artifact storage') {
      when {
        branch 'master'
      }
      steps {
        withAWS(credentials: 'AWSCredentials', region: 'eu-west-1') {
          timeout(time: 3, unit: 'MINUTES') {
            retry(count: 5) {
              s3Upload(file: ZipPackageName, bucket: AWSBucket, path: "${ProjectName}/${BRANCH_NAME}/${ZipPackageName}")
            }

          }

        }

      }
    }
  }
  environment {
    AWSBucket = 'test.axioma.internal.depolyment'
    AWSCredentials = 'AWSCredentials'
    StashedPackage = 'Package'
    ZipPackageName = 'package.zip'
    ProjectName = JOB_NAME.replace("%2F", "_")
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    timeout(time: 20, unit: 'MINUTES')
  }
}