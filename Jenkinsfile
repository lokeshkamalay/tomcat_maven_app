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
        stage('Clear Workspace'){
            steps{
                deleteDir()
            }
        }
        stage('Build'){
            steps{
                sh "mvn clean package"
            }
        }
    }
}
