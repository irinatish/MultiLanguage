pipeline {
  agent any
    tools {
        maven "Maven"
        jdk "JDK17"
    }
      environment{
		  IQ_PATH = "/opt/sonatype/iq-184/nexus-iq-cli-1.184.0-01.jar"
		  IQ_URL = "https://iq.itishelman.ngrok.io/"
	      	  IQ_USER = "eGV9kBkc"
	      	  IQ_PWD = "rBoyXtaC9tUyKZNIqvctF6qkbTln5QcA8CdSpdtq9i36"
    }
 
  stages {
    stage("List env vars"){
			steps{
				sh "printenv | sort"
			}
		}
    stage("Using env vars"){
			steps{
				echo "BUILD_NUMBER = ${env.BUILD_NUMBER}"
        sh 'echo BUILD_NUMBER = $BUILD_NUMBER'
        echo "Current IQ Path is ${env.IQ_PATH}"
	echo "Current IQ URL is ${env.IQ_URL}"
			}
		}
    stage('Pull Source') {
      // Get some code from a GitHub repository
	steps {
	echo "Begin checkout"
        git (url: 'https://github.com/irinatish/MultiLanguage.git', credentialsId: 'IrinaTishelman-GitHub', branch: 'main')
	echo "End checkout"
      }
    }
    stage('Build & Install') {
      steps {
        echo "Performing maven build"
        sh 'mvn -B -DskipTests clean package'
	sh 'pip install -r requirements.txt'
      }
    }
     stage('Nexus LC CLI Evaluation') {
       steps {
	
	catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')
	       {
	echo "Scan with Sonatype Nexus Lifecycle CLI"
	echo "java -jar ${env.IQ_PATH} -s ${env.IQ_URL} -a ${env.IQ_USER}:${env.IQ_PWD} -c -t build -i MultiLanguage-Jenkins-CLI ."
      
	sh "java -jar ${env.IQ_PATH} -s ${env.IQ_URL} -a ${env.IQ_USER}:${env.IQ_PWD} -c -t build -i MultiLanguage-Jenkins-CLI ."
    		}
	    
      }
    }
    stage('Nexus Lifecycle Evaluation') {
      steps {
        nexusPolicyEvaluation failBuildOnNetworkError: false, iqApplication: "MultiLanguage-Jenkins-pipeline", iqStage: 'build', jobCredentialsId: ''
      }
    }
    
  }
   post {
       always {
          junit(
        allowEmptyResults: true,
        testResults: '*/test-reports/.xml'
      )
      }
   } 
}
