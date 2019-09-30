pipeline {
    agent {
        docker {
            image 'cloudbees/jnlp-slave-with-java-build-tools'
            label 'docker'
        }
    }

    environment {
        nginx_repo = "lokeshkamalay/nginx:latest"
        tomcat_repo = "lokeshkamalay/tomcat"
    }

    stages{
        stage('Build'){
            steps {
                sh "mvn clean package"
            }
        }
    }
}
