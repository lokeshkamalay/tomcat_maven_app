pipeline {
    agent {
        label 'maven'
    }
    tools {
        maven 'maven339'
        jdk 'oracle-jdk-1.8.221'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '1'))
        timeout (time: 20, unit: 'SECONDS')
    }
    environment {
        VAR1 = "Test Variable"
    }
    stages{
        stage('Build'){
            steps{
                sh "mvn clean package"
            }
        }

        stage('Print Public Key'){
            agent {
                label 'test'
            }
            input {
                message 'Do You want me to continue?'
                ok 'Yes'
            }
            steps{
                sh "touch abc.txt"
            }
        }
    }
    post {
        always {
            echo "The job is successful"
            deleteDir()
        }
        failure {
            echo "Shoot an email"
        }
    }
}
