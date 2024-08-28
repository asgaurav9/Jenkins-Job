# Jenkins-Job

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building the project...'
                // Use Maven for build
                sh 'mvn clean package'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running Unit and Integration Tests...'
                // Use JUnit for unit testing
                sh 'mvn test'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Analyzing Code...'
                // Use SonarQube for code analysis
                sh 'mvn sonar:sonar'
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Performing Security Scan...'
                // Use OWASP Dependency Check for security scan
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to Staging...'
                // Deploy to staging server, e.g., AWS EC2 instance
                sh 'scp target/*.jar user@staging-server:/path/to/deploy'
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Running Integration Tests on Staging...'
                // Run integration tests on staging
                sh 'ssh user@staging-server "java -jar /path/to/deploy/app.jar --test"'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploying to Production...'
                // Deploy to production server, e.g., AWS EC2 instance
                sh 'scp target/*.jar user@production-server:/path/to/deploy'
            }
        }
    }

    post {
    always {
        echo 'Pipeline completed.'
    }
    success {
        echo 'Pipeline succeeded!'
    }
    failure {
        echo 'Pipeline failed.'
    }
    stage('Unit and Integration Tests') {
        steps {
            echo 'Running Unit and Integration Tests...'
            // Unit Test Tool: JUnit
            sh 'mvn test'
        }
        post {
            always {
                emailext(
                    subject: "Unit and Integration Test Results: ${currentBuild.currentResult}",
                    body: "Please find the attached test logs.",
                    to: 'your-email@example.com',
                    attachLog: true
                )
            }
        }
    }
    stage('Security Scan') {
        steps {
            echo 'Performing Security Scan...'
            // Security Scan Tool: OWASP Dependency Check
            sh 'mvn org.owasp:dependency-check-maven:check'
        }
        post {
            always {
                emailext(
                    subject: "Security Scan Results: ${currentBuild.currentResult}",
                    body: "Please find the attached security scan logs.",
                    to: 'your-email@example.com',
                    attachLog: true
                )
            }
        }
    }
}
