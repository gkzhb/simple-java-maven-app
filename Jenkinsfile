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
                sh './jenkins/scripts/deliver.sh'
            }
        }
        stage('Connect Remote') {
            steps {
                sshagent(credentials: ['deploy']) {
                    sh 'ssh -o StrictHostKeyChecking=no root@gkzhb.tk echo "hello world!"'
                }
            }
        }
    }
}
