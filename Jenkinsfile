pipeline {
    agent {
        docker {
            image 'maven:3.8.1-adoptopenjdk-11' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }

	stage('SonarQube analysis') {
    		def scannerHome = tool 'SonarScanner 4.7';
    		withSonarQubeEnv('SonarCloud') { // If you have configured more than one global server connection, you can specify its name
      		sh "${scannerHome}/bin/sonar-scanner"
    		}
  	}

       stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
	stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
