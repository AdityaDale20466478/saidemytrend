def registry = 'https://trial5wytp0.jfrog.io'

pipeline {
    agent any

    tools {
        maven 'maven'   // use configured Maven tool if available
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage("Build") {
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean deploy -DskipTests'
                echo "----------- build completed ----------"
            }
        }

        stage("Test Report") {
            steps {
                echo "----------- unit test report ----------"
                sh 'mvn surefire-report:report'
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'saidemy-soanr-scanner'
            }
            steps {
                withSonarQubeEnv('Saidemy-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage("Publish Build Info") {
            steps {
                script {
                    echo '<--------------- Build Info Publish Started --------------->'
                    def server = Artifactory.newServer(
                        url: registry + '/artifactory',
                        credentialsId: 'artifact-cred'
                    )
                    def buildInfo = Artifactory.newBuildInfo()
                    buildInfo.env.capture = true
                    server.publishBuildInfo(buildInfo)
                    echo '<--------------- Build Info Publish Ended --------------->'
                }
            }
        }
    }
}

