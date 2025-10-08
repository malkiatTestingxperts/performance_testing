pipeline {
    agent any

    stages {
      
      stage('Prepare Git') {
    steps {
        bat 'git config --system core.longpaths true'
    }
          }
        stage('Checkout Code') {
            steps {
                cleanWs() // Clean workspace before checkout
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/malkiatTestingxperts/performance_testing.git'
                    ]],
                     extensions: [
                [$class: 'CheckoutOption', timeout: 30],
                [$class: 'CleanBeforeCheckout'],
                [$class: 'CloneOption', depth: 1, shallow: true]
            ]
                ])
            }
        }
      
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Artillery Test') {
            steps {
                bat 'npx artillery run load-test.yml --record --key a9_q4g7gmk9hbvacn1juvje9b2d3yekjhw6 -o report.json'
            }
        }

        stage('Archive Results') {
            steps {
                archiveArtifacts artifacts: 'report.json', fingerprint: true
            }
        }

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
        echo 'Cleaning up workspace'
        cleanWs()
    }
}
}
