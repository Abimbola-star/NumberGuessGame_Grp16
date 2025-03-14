pipeline {
    agent {
        label 'Maven'
    }
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
                sh 'mvn clean install'
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
            emailext body: 'Check console output at $BUILD_URL to view the results.',
            subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!',
            to: 'ayoyinka.zapier@gmail.com'
        }
    }
}
