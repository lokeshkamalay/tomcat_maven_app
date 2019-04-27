pipeline{
    agent none

    environment{
        SONARQUBE_URL = 'http://138f85f2.ngrok.io'
        SONARQUBE_LOGIN = credentials('sonar-login')
        VM_USER = "ec2-user"
        VM_SERVER = "172.31.85.9"
        TAG = "lokeshkamalay/tomcat"
        PORT = 8080

    }
    parameters {
        choice(choices: 'Yes\nNo', description: 'Pls choose the environment to deploy', name: 'Deploy')
        choice(choices: 'Yes\nNo', description: 'Deploy to Docker', name: 'DeployDocker')
    }
    // tools{}
    // options{}
    stages{
        stage('Junit and Sonar'){
            agent {
                docker {
                    image 'maven:3-jdk-8'
                    label 'dockernode'
                }
            }
            steps {
                sh 'mvn --settings settings.xml test verify surefire-report:report-only sonar:sonar -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=$SONARQUBE_LOGIN'
            }
            post {
                success {
                    junit "target/surefire-reports/*.xml"
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'target/site', reportFiles: 'surefire-report.html', reportName: 'SureFireReportHTML', reportTitles: ''])
                }
            }

        }

        stage('Build'){
            agent {
                dockerfile {
                    filename 'Dockerfile-project'
                    label 'dockernode'
                }
            }
            stages{
                stage('Read the POM'){
                    steps{
                        script{
                            pom = readMavenPom file: 'pom.xml'
                            ARTIFACT_NAME = pom.artifactId.toString()
                        }

                    }
                }
                stage('Packaging'){
                    steps{
                        sh 'mvn deploy --settings settings.xml -DSkipTests=true'
                        stash includes: "target/${ARTIFACT_NAME}.war", name: 'artifact'
                    }
                    post {
                        success {
                            echo "Package has been uploaded to Artifactory Successfully"
                        }
                    }
                }
            }
        }
        stage('Deployment'){
            when {
                beforeAgent true
                expression { params.Deploy == 'Yes'}
            }
            parallel {
                stage('To VM'){
                    agent{ label 'dockernode' }
                    steps{
                        sshagent(['vm-private-key']){
                            unstash 'artifact'
                            sh "scp -o StrictHostKeyChecking=no -r target/${this.ARTIFACT_NAME}.war ${this.VM_USER}@${this.VM_SERVER}:/tmp"
                            sh "ssh -o StrictHostKeyChecking=no ${this.VM_USER}@${this.VM_SERVER} 'cp -r /usr/share/tomcat/webapps/ROOT/ /usr/share/tomcat/webapps/ROOT_Backup/; rm -rf /usr/share/tomcat/webapps/ROOT/; unzip /tmp/*.war -d /usr/share/tomcat/webapps/ROOT; rm -rf /tmp/*.war'"
                        }
                    }
                }
                stage('To Image'){
                    agent{ label 'dockernode'}
                    steps{
                        script{
                            unstash 'artifact'
                            docker.withRegistry('','docker-hub') {
                            customImage = docker.build("${TAG}:${env.BUILD_ID}")
                            customImage.push()
                            }
                        }
                    }
                }
            }
        }
        stage('Deploying To Docker Environment'){
            when {
                beforeAgent true
                expression { params.DeployDocker == 'Yes'}
            }
            agent{ label 'dockernode'}
            steps{
                sh """
                    docker run -d -p $PORT:8080 $TAG:$BUILD_ID
                """
            }
        }
    }
}
