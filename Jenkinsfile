pipeline {
    agent none //Since I'm using two different agents below

    environment { //This is how you mention env vars
        nginx_repo = "lokeshkamalay/nginx:latest"
        tomcat_repo = "lokeshkamalay/tomcat"
        ARTIFACT_NAME = "java-tomcat-maven-example"
    }

    stages {
        stage('Checking Out and Build'){
            agent { //Building agent using a Dockerfile, this will build the image first using the given docker and then runs the steps inside it
                dockerfile {
                    filename 'Dockerfile-maven'
                    label 'docker'
                }
            }
            steps {
                checkout scm
                sh "mvn clean package"
                stash includes: "target/${ARTIFACT_NAME}.war", name: 'artifact' //copying the required file onto master
            }
        }
        stage('Docker Build and Push'){
            agent { 
                label 'docker' //running another stage on diff node (assume)
            }
            steps {
                unstash 'artifact' //unstashing will copy the file here 
                script {
                    def customImage = docker.build("$tomcat_repo:${env.BUILD_ID}") //docker login is having some problem in my machine so no login is performed here
                    echo "Before push"
                    customImage.push()
                }
            }
        }
    } 
}
