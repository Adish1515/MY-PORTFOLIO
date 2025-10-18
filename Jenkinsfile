pipeline {
  agent any

  environment {
    S3_BUCKET = "arn:aws:s3:::adish-portfolio-site"
  }

  stages {
    stage('Checkout') {
      steps {
        sshagent(['gitkey']) {
          git url: 'git@github.com:Adish1515/MY-PORTFOLIO.git', branch: 'main'
        }
      }
    }

    stage('Build') {
      steps {
        // run build if needed
        sh 'ls -la'
      }
    }

    stage('Sync to S3') {
      steps {
        withCredentials([
          string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'adish-key.pem'),
          string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'adish-key.pem')
        ]) {
          sh '''
            export AWS_ACCESS_KEY_ID=${adish-key.pem}
            export AWS_SECRET_ACCESS_KEY=${adish-key.pem}
            aws s3 sync . s3://${S3_BUCKET} --delete --exclude ".git/*" --acl public-read
          '''
        }
      }
    }
  }

  post {
    success { echo "S3 sync done" }
    failure { echo "S3 sync failed" }
  }
}



