pipeline {
    agent any
    triggers { cron('H/5 * * * 4') }
    stages {
        stage('Checkout') { steps { checkout scm } }
        stage('Test & Coverage') {
            steps { 
                bat 'mvn clean test jacoco:report'
            }
            post {
                always {
                    junit '**/surefire-reports/*.xml'
                    jacoco execPattern: 'target/jacoco.exec'
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target/site/jacoco',
                        reportFiles: 'index.html',
                        reportName: 'JaCoCo Report'
                    ])
                }
            }
        }
        stage('Package Artifact') {
            steps {
                bat 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
        }
    }
}
