pipeline {
    agent any
    tools {
        jdk 'JDK11'
        maven 'Maven3'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'Master', credentialsId: 'github-token', url: 'https://github.com/nramana050/DevOps-Engineer--Assignment.git' 
                
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
            emailext(
                subject: "Build ${currentBuild.fullDisplayName} - ${currentBuild.result}",
                body: "Build result: ${currentBuild.result}. See console output: ${env.BUILD_URL}",
                recipientProviders: [culprits()]
            
        }
        failure {
            slackSend(color: 'danger', message: "Build ${currentBuild.fullDisplayName} failed: ${env.BUILD_URL}")
        
    }
}
