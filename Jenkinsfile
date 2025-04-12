pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }
    stages {
        stage('Build') {
            steps {
				echo "-------build started----"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
				echo "--build completed---"
            }
        }
		stage('test'){
			steps{
				echo "-----unit test started----"
				sh 'mvn surefire-report:report'
				echo "-----unit test completed---"
			}
		}
        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'saideny-sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
