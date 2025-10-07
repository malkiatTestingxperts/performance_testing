pipeline {
    agent any

    stages {
        // Stage 1: Checkout Code
        stage('Checkout Code') {
            steps {
                cleanWs() // Clean workspace before checkout
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/malkiatTestingxperts/performance_testing.git'
                    ]],
                    extensions: [[$class: 'CheckoutOption', timeout: 30]]
                ])
            }
        }

        // Stage 2: Install Dependencies
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        // Stage 3: Run Artillery Test
        stage('Run Artillery Test') {
            steps {
                bat 'npx artillery run load-test.yml --record --key a9_q4g7gmk9hbvacn1juvje9b2d3yekjhw6 -o report.json'
            }
        }

        // Stage 4: Archive Results
        stage('Archive Results') {
            steps {
                archiveArtifacts artifacts: 'report.json', fingerprint: true
            }
        }

        // Stage 5: Check Results
        stage('Check Results') {
            steps {
                script {
                    def results = readJSON file: 'report.json'
                    def errors = results.aggregate?.codes?.get("400") ?: 0
                    if (errors > 0) {
                        error "Test failed with ${errors} bad responses"
                    } else {
                        echo "Test passed successfully!"
                    }
                }
            }
        }
    }

    post {
        always {
                   steps {
            echo 'Cleaning up workspace...'
            cleanWs()
            }
        }
    }
}
