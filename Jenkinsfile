pipeline {
  agent any
  environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	    }
  tools {
        jdk "JDK 11"
    }
    stages {
      stage('1: Download') {
        steps{
            script{
                echo "Clean first"
                sh 'rm -rf *'
                echo "Download from source."
                sh 'git clone https://github.com/contraster-steve/ticketbook'
                }
            }
        }
    stage('2: Add Contrast') {
        steps{
            withCredentials([string(credentialsId: 'AUTH_HEADER', variable: 'AUTH_HEADER'), string(credentialsId: 'API_KEY', variable: 'api_key'), string(credentialsId: 'SERVICE_KEY', variable: 'service_key'), string(credentialsId: 'USER_NAME', variable: 'user_name')]) {
                script{
                    echo "Edit YAML."
                    dir('./ticketbook/') {
                        sh 'echo "api:\n  url: https://apptwo.contrastsecurity.com/Contrast\n  api_key: $api_key\n  service_key: $service_key\n  user_name: $user_name\nagent:\n  java:\n    standalone_app_name: Ticketbook\napplication:\n  metadata: "bU=PS, contact=steve.smith@contrastsecurity.com"\n  session_metadata: "buildNumber=${BUILD_NUMBER}, committer=Steve Smith"\n  version: ${JOB_NAME}-${BUILD_NUMBER}\nserver:\n  name: Application-Server-dev\n  environment: dev\nassess:\n  enable: true\nprotect:\n  enable: false" >> /opt/contrast/contrast_security.yaml'
                        sh 'chmod 755 /opt/contrast/contrast_security.yaml'
                        }
                    sh 'curl -o ./ticketbook/contrast.jar https://repo1.maven.org/maven2/com/contrastsecurity/contrast-agent/4.1.0/contrast-agent-4.1.0.jar'
                }
            }
        }
      }            
      stage('3: Build Images') {
        steps{
            script{
                echo "Build Ticketbook."
                dir('./ticketbook/') {
                    sh 'mvn -U clean package'
                    }
                }
            }
        }        
    stage('4: Deploy') {
        steps{
            script{
            echo "Run Dev here."
            dir('./ticketbook/') {
                sh 'sudo cp ./target/ticketbook.war /opt/tomcat/webapps/'
                sh 'sudo systemctl restart tomcat'
                    }
                }
            }
        }
    }
}    
