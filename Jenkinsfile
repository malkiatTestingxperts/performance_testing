pipeline {
  agent any

  tools {
    nodejs "Node22" // Make sure NodeJS tool is configured in Jenkins
  }

  stages {
    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Artillery Test') {
      steps {
        sh 'npx artillery run load-test.yml -o report.json'
      }
    }

    stage('Archive Results') {
      steps {
        archiveArtifacts artifacts: 'report.json', fingerprint: true
      }
    }

    // // Optional stage: fail build if threshold is exceeded
    // stage('Check Results') {
    //   steps {
    //     script {
    //       def results = readJSON file: 'report.json'
    //       def errors = results.aggregate?.codes?.["400"] ?: 0
    //       if (errors > 0) {
    //         error " Test failed with ${errors} bad responses"
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
