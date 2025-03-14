pipeline {
    agent any  // This sets the agent to run on the Jenkins server (master node)

    tools {
        maven 'mvn'
        jdk 'java'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/Abimbola-star/NumberGuessGame_Grp16.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Get the WAR file name dynamically from the target directory
                    def warFile = sh(script: "ls target/*.war | head -n 1", returnStdout: true).trim()

                    // Define the new WAR file name with the build number
                    def newWarFile = "${warFile.replaceAll('.war', '')}-${BUILD_NUMBER}.war"

                    // Rename the WAR file to include the Jenkins build number
                    sh "mv ${warFile} ${newWarFile}"

                    // Move the WAR file to Tomcat's webapps folder
                    sh "sudo mv ${newWarFile} /opt/apache-tomcat-9.0.100/webapps/"
                    
                    // Restart Tomcat to deploy the new WAR file
                    sh "sudo /opt/apache-tomcat-9.0.100/bin/shutdown.sh && sudo /opt/apache-tomcat-9.0.100/bin/startup.sh"
                }
            }
        }
    }
    post {
        always {
            emailext(
                body: """
                    <h3>Build Notification</h3>
                    <p>Build <strong>${BUILD_NUMBER}</strong> of <strong>${JOB_NAME}</strong> finished with status: <strong>${BUILD_STATUS}</strong></p>
                    <p>Check console output at <a href="${BUILD_URL}">this link</a> to view the results.</p>
                """,
                subject: "Build # ${BUILD_NUMBER} - ${JOB_NAME} - ${BUILD_STATUS}",
                to: 'yimikacreations@gmail.com'
            )
        }
    }
}
