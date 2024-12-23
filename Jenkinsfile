pipeline {
    agent any
    tools {
        jdk 'JDK11'
        maven 'Maven3'
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Compile Code') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
                junit '**/target/surefire-reports/*.xml'
            }
        }
        stage('Code Coverage - JaCoCo') {
            steps {
                sh 'mvn jacoco:report'
                publishHTML(target: [
                    reportDir: 'target/site/jacoco',
                    reportFiles: 'index.html',
                    reportName: 'Code Coverage'
                ])
            }
        }
        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Cyclomatic Complexity') {
            steps {
                sh 'lizard src/main/java'
            }
        }
        stage('Security Vulnerability Scan') {
            steps {
                sh 'dependency-check --scan ./ --out ./dependency-check-report'
                archiveArtifacts 'dependency-check-report/*'
            }
        }
    }
    post {
        always {
            mail to: 'nramana971@example.com',
                 subject: "Build ${currentBuild.fullDisplayName}",
                 body: "Check Jenkins for details."
            slackSend(channel: '#your-channel', message: "Build ${currentBuild.fullDisplayName} completed. Check Jenkins for details.")
        }
        success {
            mail to: 'nramana971@example.com',
                 subject: "Build Success: ${currentBuild.fullDisplayName}",
                 body: "The build was successful. Check Jenkins for details."
            slackSend(channel: '#your-channel', message: "Build Success: ${currentBuild.fullDisplayName}")
        }
        failure {
            mail to: 'nramana971@example.com',
                 subject: "Build Failed: ${currentBuild.fullDisplayName}",
                 body: "The build failed. Please check Jenkins for details."
            slackSend(channel: '#your-channel', message: "Build Failed: ${currentBuild.fullDisplayName}. Please check Jenkins for details.")
        }
    }
}
