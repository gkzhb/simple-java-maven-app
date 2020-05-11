pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
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
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'ali',
                                transfers: [
                                    sshTransfer(
                                        execCommand: 'pwd',
                                    ),
                                    sshTransfer(
                                        cleanRemote: false,
                                        excludes: '',
                                        execTimeout: 120000,
                                        flatten: false,
                                        makeEmptyDirs: false,
                                        noDefaultExcludes: false,
                                        patternSeparator: '[, ]+',
                                        remoteDirectory: './simple-java',
                                        remoteDirectorySDF: false,
                                        removePrefix: '',
                                        sourceFiles: './Jenkinsfile'
                                    )
                                ],
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false,
                                verbose: true
                            )
                        ]
                    )
                }
            }
        }
		stage('Deploy') {
			script {
				if(currentBuild.currentResult == "SUCCESS" || currentBuild.currentResult == "UNSTABLE") {
					sh 'echo "Build Succeeded."'
				}
			}
		}
    }
}
