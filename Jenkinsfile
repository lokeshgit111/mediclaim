pipeline {
   agent any
	stages {
      stage('Git Checkout') {
         steps {
            git credentialsId: 'e64d269b-6890-4f5b-bb3f-37a725e080ab', url: 'https://github.com/lokeshgit111/mediclaim.git'
		}
	}
	stage('Build') {
		steps {
			withSonarQubeEnv('sonar-webhook') {
				sh '/opt/maven/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=true'
			}
		}
	}
	stage("Quality Gate") {
            steps {
		    sh "sleep 15"
              timeout(time: 2, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
		
		
		
	stage ('nexus') {
            steps {
                     nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'sampleapp-release', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '.jar', filePath: 'target/mediclaim-0.0.13-SNAPSHOT.jar']], mavenCoordinate: [artifactId: 'mediclaim', groupId: 'org.springframework.boot', packaging: 'jar', version: '2.2.2.RELEASE']]]
                }
            }
       
	
		
		
		
		
		
	stage ('Deploy') {
		steps {
			sh '/opt/maven/bin/mvn clean deploy -Dmaven.test.skip=true'
		}
	}
		
		
	
	
		
		
		
		
		
	stage ('Release') {
		steps {
			sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
		}
	}
	//stage ('DB Migration') {
	//	steps {
	//		sh '/usr/share/maven/bin/mvn clean flyway:migrate'
		}
	}
//}
	post {
        always {
            emailext body: "${currentBuild.currentResult}: Project Name : ${env.JOB_NAME} Build ID : ${env.BUILD_NUMBER}\n\n Approval Link :  ${env.BUILD_URL}", recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
        }
    }

//}
