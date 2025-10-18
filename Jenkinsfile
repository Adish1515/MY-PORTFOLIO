pipeline {
  agent any

  environment {
    S3_BUCKET = "arn:aws:s3:::adish-portfolio-site"
  }

  stages {
    stage('Checkout') {
      steps {
        sshagent(['gitkey']) {
          git url: 'git@github.com:<yourusername>/<yourrepo>.git', branch: 'main'
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
          string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
          string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
        ]) {
          sh '''
            export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
            export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
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


