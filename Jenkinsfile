pipeline {
  agent any

  tools {
    nodejs "Node22" // Ensure NodeJS tool is configured in Jenkins (Manage Jenkins > Tools)
  }

  stages {
    stage('Checkout Code') {
      steps {
        cleanWs() // 🧹 Clean old files before pulling new code
        // Custom checkout with 30-min timeout
        checkout([$class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[
            url: 'https://github.com/malkiatTestingxperts/performance_testing.git'
          ]],
          extensions: [[$class: 'CheckoutOption', timeout: 30]]
        ])
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Artillery Test') {
      steps {
        // Running Artillery load test for your target YAML file
        sh 'npx artillery run load-test.yml -o report.json'
      }
    }

    stage('Archive Results') {
      steps {
        archiveArtifacts artifacts: 'report.json', fingerprint: true
      }
    }

    // Optional stage to validate results
    // stage('Check Results') {
    //   steps {
    //     script {
    //       def results = readJSON file: 'report.json'
    //       def errors = results.aggregate?.codes?.["400"] ?: 0
    //       if (errors > 0) {
    //         error "Test failed with ${errors} bad responses"
    //       } else {
    //         echo "Test passed successfully!"
    //       }
    //     }
    //   }
    // }
  }

  post {
    always {
      echo 'Cleaning up workspace...'
      cleanWs()
    }
  }
}
