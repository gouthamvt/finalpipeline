pipeline {
 agent { label 'hostgvt' }
 tools {
        jdk 'JDK8gvtlocal'
        maven 'linuxhostmaven'   
     }
  stages {
    
	stage('Source') { 
      steps {
checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/gouthamvt/happy-trip.git']]])              }
    }
	
     stage('Compile') { 
          
	    steps{
		    
        sh 'java -version'
        sh 'mvn -version'
        sh 'mvn clean package'
	
		}
	 
    }

	
	stage('SonarScan and notify by email') {

        steps{
		        withSonarQubeEnv(installationName: 'SonarQube'){
                sh 'mvn clean package sonar:sonar'
		    
            }
        }
    }
	
  }
	post
	{
		always{
             emailext attachLog: true, body: '''$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:

Check console output at $BUILD_URL to view the results of the build.''', compressLog: true, subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: '$DEFAULT_RECIPIENTS,pratdeus@gmail.com'
			   
		}
		always{
		    chuckNorris()
		}
	}
	

}
