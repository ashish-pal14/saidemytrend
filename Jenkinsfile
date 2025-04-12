pipeline{
	agent ant
	environment{
		PATH='/opt/maven/bin:$PATH'
	}
	stages{
		stage ('build'){
			steps{
				sh 'mvn clean deploy'
			}
		}
		stage('SOnarQube Analysis'){
			environment {
				scannerHome=tool 'saideny-sonar-scanner'
			}
			steps{
				withSonarQubeEnv('sonarqube')
				sh "{$scannerHome}/bin/sonar-scanner"
			}
		}
	}
}
