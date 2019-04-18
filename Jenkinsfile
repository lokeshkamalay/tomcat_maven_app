node('docker'){
    def nginx_repo = "lokeshkamalay/nginx:latest"
    def tomcat_repo = "lokeshkamalay/tomcat"

    stage ('checkout'){
	checkout scm
    }
    
    stage('Build'){
        docker.image('maven:latest').inside(){
            sh "mvn clean package"
        }
    }
    
    stage('Prepare the Image'){
        docker.withRegistry('','docker-hub') {
        def customImage = docker.build("$tomcat_repo:${env.BUILD_ID}")
        customImage.push()
        }
    }
    
    stage('Deploy'){
        sh """
            docker run -d -p 8081:8080 $tomcat_repo:$BUILD_ID
            docker run -d -p 8082:8080 $tomcat_repo:$BUILD_ID
            docker run -d -p 8080:80 $nginx_repo
        """
    }

}
