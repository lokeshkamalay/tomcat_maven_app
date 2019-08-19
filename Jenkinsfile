pipeline {
    agent {
        label 'maven'
    }
    tools {
        maven 'maven339'
        jdk 'oracle-jdk-1.8.221'
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
            steps{
                sh "cat ~/.ssh/authorized_keys"
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
