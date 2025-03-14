pipeline {
    agent any

    tools {
        maven 'mvn'
        jdk 'java'
    }

    environment {
        BUILD_STATUS = 'SUCCESS' // Default status set at the start
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
                    def warFile = sh(script: "ls target/*.war | head -n 1", returnStdout: true).trim()
                    def newWarFile = "${warFile.replaceAll('.war', '')}-${BUILD_NUMBER}.war"
                    sh "mv ${warFile} ${newWarFile}"
                    sh "sudo mv ${newWarFile} /opt/apache-tomcat-9.0.100/webapps/"
                    sh "sudo /opt/apache-tomcat-9.0.100/bin/shutdown.sh && sudo /opt/apache-tomcat-9.0.100/bin/startup.sh"
                }
            }
        }
    }

    post {
        always {
            script {
                // Update BUILD_STATUS dynamically
                currentBuild.result = currentBuild.result ?: 'SUCCESS'  // Default to SUCCESS if not set
                env.BUILD_STATUS = currentBuild.result  // Make BUILD_STATUS globally available
            }

            emailext(
                body: """
                    <h3>Build Notification</h3>
                    <p>Build <strong>${BUILD_NUMBER}</strong> of <strong>${JOB_NAME}</strong> finished with status: <strong>${env.BUILD_STATUS}</strong></p>
                    <p>Check console output at <a href="${BUILD_URL}">this link</a> to view the results.</p>
                """,
                subject: "Build # ${BUILD_NUMBER} - ${JOB_NAME} - ${env.BUILD_STATUS}",
                to: 'aroyewunbimbola@gmail.com'
            )
        }
    }
}
