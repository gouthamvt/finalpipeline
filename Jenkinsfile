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

      /* stage('Nexus Artifact Uploader')
	{
	steps{
		nexusArtifactUploader artifacts: [[artifactId: 'prabhav-1', classifier: '', file: 'target/prabhav-1.war', type: 'war']], credentialsId: '1fd13c5e-9ac7-4f07-9b47-2b60acc81c81', groupId: 'nexus.happytrip', nexusUrl: '172.31.12.84:9030/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'test123', version: '${BUILD_NUMBER}'
		}
			}
*/
 stage('Nexus Artifact Publisher')
	{
	steps{
		nexusPublisher nexusInstanceId: 'nexus-123', nexusRepositoryId: 'test123', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: 'war', filePath: 'target/prabhav-1.war']], mavenCoordinate: [artifactId: 'prabhav-1', groupId: 'nexus.happytrip', packaging: 'war', version: '${BUILD_NUMBER}']]]
		}
			}

stage ('Approval'){
			agent none
            steps{
                script {
                        approved = input message: 'Release to production?', ok: 'Yes', submitter: 'pm'
    					if (approved) {
    						withCredentials([usernamePassword(credentialsId: 'privilegedCreds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
    						echo "Approved"
    					        		}
    					}
    			}
            }
        }


	
  }
	post
	{
		always{
steps{
deploy adapters: [tomcat7(path: '', url: 'http:\\172.31.12.84:9050')], contextPath: '/app', war: 'http:\\172.31.12.84:9030\nexus/content/repositories/test123/nexus/happytrip/prabhav-1/${BUILD_NUMBER}/prabhav-1-${BUILD_NUMBER}.war'
             
}
		}
changed{
emailext attachLog: true, body: '''$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:

Check console output at $BUILD_URL to view the results of the build.''', compressLog: true, subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: '$DEFAULT_RECIPIENTS,pratdeus@gmail.com'
		}
success{ chuckNorris()}
		

	
	}
	

}
