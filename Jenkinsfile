pipeline {
  agent any

  tools {
    nodejs "Node22" // NodeJS must be configured in Jenkins
  }

  stages {
    // stage('Checkout Code') {
    //   steps {
    //     cleanWs()
    //     checkout([$class: 'GitSCM',
    //       branches: [[name: '*/main']],
    //       userRemoteConfigs: [[
    //         url: 'https://github.com/malkiatTestingxperts/performance_testing.git'
    //       ]],
    //       extensions: [[$class: 'CheckoutOption', timeout: 30]]
    //     ])
    //   }
    // }

    stage('Install Dependencies') {
      steps {
        bat 'npm install'
      }
    }

    stage('Run Artillery Test') {
      steps {
        bat 'npx artillery run load-test.yml -o report.json'
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
      echo 'Cleaning up workspace...'
      cleanWs()
    }
  }
}
