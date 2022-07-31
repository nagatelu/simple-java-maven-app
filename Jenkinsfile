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

	stage('Build and SonarQube analysis') {
            steps {
                withSonarQubeEnv('SonarCloud') {
                    // Optionally use a Maven environment you've configured already
                    //withMaven(maven:'Maven 3.5') {
                        sh 'mvn clean package sonar:sonar -Dsonar.projectKey=javaprojectnaga -Dsonar.organization=javaprojectnaga'
                   // }
                }
            }
        }
	//stage("Quality Gate") {
        //    steps {
        //      timeout(time: 1, unit: 'HOURS') {
        //        waitForQualityGate abortPipeline: true
        //      }
        //    }
        //  }

 	stage('SCA-Snyk') {
	      steps {
        	echo 'Testing...'
        	snykSecurity(snykInstallation: 'SNYK_TOKEN',snykTokenId: 'SNYK_TOKEN')
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
