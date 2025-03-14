pipeline {
    agent {
        label 'Maven'
    }
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
                stash(name: 'packaged_code', includes: 'target/*.war')
            }
        }

        stage('Deploy') {
            agent {
                label 'Tomcat'
            }
            steps {
                unstash 'packaged_code'
                script {
                    // Get the WAR file name dynamically from the target directory
                    def warFile = sh(script: "ls target/*.war | head -n 1", returnStdout: true).trim()

                    // Define the new WAR file name with the build number
                    def newWarFile = "${warFile.replaceAll('.war', '')}-${BUILD_NUMBER}.war"

                    // Rename the WAR file to include the Jenkins build number
                    sh "mv ${warFile} ${newWarFile}"

                    // Move the WAR file to Tomcat's webapps folder (since it's the default remote root directory)
                    sh "mv ${newWarFile} /opt/apache-tomcat-9.0.100/webapps/"

                    // Restart Tomcat to deploy the new WAR file
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
