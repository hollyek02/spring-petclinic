pipeline {
    agent any
    tools {
        maven 'Maven'  
    }
    triggers {
        cron('H/5 * * * 4')  
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Test & Coverage') {
            steps {
                bat 'mvn clean test jacoco:report'
            }
            post {
                always {
                    
                    script {
                        try {
                            junit '**/surefire-reports/*.xml'
                        } catch (e) { echo 'No JUnit reports' }
                    }
                    jacoco(
                        execPattern: 'target/jacoco.exec',
                        classPattern: 'target/classes',
                        sourcePattern: 'src/main/java',
                        exclusionPattern: 'src/test*'
                    )
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target/site/jacoco',
                        reportFiles: 'index.html',
                        reportName: 'JaCoCo Coverage Report'
                    ])
                }
            }
        }
        stage('Package Artifact') {
            steps {
                bat 'mvn package -DskipTests'
                archiveArtifacts(
                    artifacts: 'target/*.jar',
                    fingerprint: true,
                    allowEmptyArchive: false
                )
            }
        }
    }
    post {
        always {
            cleanWs()  // Cleanup workspace
        }
    }
}
