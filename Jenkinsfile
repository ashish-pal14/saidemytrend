def registry='https://trialjukrcb.jfrog.io/'

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

        stage('Test') {
            steps {
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

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }

        stage('Jar Publish') {
            steps {
                script {
                    echo '----------Jar publish----------'

                    def server = Artifactory.newServer url: "https://your-registry-url/artifactory", credentialsId: "276c325e-8e74-4320-8770-caff3f4e64be"
                    def properties = "buildid=${env.BUILD_ID},commitid=${env.GIT_COMMIT}"

                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstring/(*)",
                                "target": "sai-libs-release-local/{1}",
                                "flat": false,
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }"""

                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)

                    echo '------jar publish end------'
                }
            }
        }
    }
}
