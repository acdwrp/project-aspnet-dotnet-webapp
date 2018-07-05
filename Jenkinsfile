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
        echo 'Deploying....'
        script {
          def files = findFiles(glob: 'SampleWebApplication/bin/*.*')



          files.each {println "RPM:  ${it}"}
          
          zip glob:'SampleWebApplication/bin/*.*', zipFile: 'test.zip'
         
          
          
          withAWS(credentials:'AWSCredentials') 
          {
               s3Download(file:'sample', bucket:'test.axioma.internal.depolyment', path:'sample', force:true)
          }
          
        }
        
        withAWS(credentials:'AWSCredentials') 
          {
               s3Download(file:'sample', bucket:'test.axioma.internal.depolyment', path:'sample', force:true)
          }

        archiveArtifacts(artifacts: 'SampleWebApplication/*.*,SampleWebApplication/bin/*.*', onlyIfSuccessful: true, fingerprint: true)
      }
    }
  }
}
