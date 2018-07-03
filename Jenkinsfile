pipeline {
  
  
  agent any {
    node {
      customWorkspace "ws\\${JOB_NAME.replace("%2F", "_")}"
    }
  }
  
  
  stages {
    
    
    stage('Build1') {
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
            }
        }
  }
}
