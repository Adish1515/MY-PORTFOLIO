pipeline {
  agent any

  environment {
    S3_BUCKET = "adish-portfolio-site"
  }

  stages {
    stage('Checkout') {
      steps {
        sshagent(['ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCv7FLddhgRYqP7Ke7D/VDZk848gUGubBOT1ghb173nqZLnXUAmDxXWbVAfo4DLiaxAIbEtkxbEmZQdXTqBmyx7kmTxvRnQoMfCCjWV4Tmg+P/L5okxLOIweI5a0ouC1gl4Oz+W0LT8nh9zxAzx/lK3+AV1WlcSUDaSm3k9zVzDRQaKhtm+Ernnqh61VW2VwhPagLmCAhOXqz1HcxYRfswlKz+o9n7OyXNqYHm6C67/GE3jwYiGbBtUZWiCNUwKrYhQzDGLdOKqGoCJpAppIfJwdfq2CnbKExP1PIbrZ/Tn2cgceVNqEqP+omcS0xOl0r64z9JESb7NYdmcbUUEsQRQzvC0XhQJDQsM8dKVw3xBZV2x1RKG7qtTJZAvQGRkaDhBT3n0iRMe5+6VEz6wOC3Hbot6fhFUxYcNYwRR1tTPj3armDfbQpFkAZwhw5VSrVzo4KJ6MkqPzkt1d/nhn6hWzMHAA6F+p84/8gpeXlECnkcK0Z3b7o4GfxyOPF10AmysCi1S1P+gVD+PaWXv9yFBzMlnYohDRgQmU7oMUVrpmgEXhkcnWXMisWbjk02wt5L7LKBv4cSHJClv6jTyHghJCckYwZld1ZO1ZajbmoYczDrg5UlRylx7heAJlWqOTlXjPzwSln9YsfvQuy8uwdpr9lfKBCTyRXbNKxJLWfar8Q== jenkins@ec2']) {   // ✅ Use your Jenkins SSH credential ID here
          git branch: 'master',      // ✅ Change 'main' to 'master' (your actual branch)
              url: 'git@github.com:Adish1515/MY-PORTFOLIO.git'
        }
      }
    }

    stage('Build') {
      steps {
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
            aws configure set aws_access_key_id $adish-key.pem
            aws configure set aws_secret_access_key $adish-key.pem
            aws s3 sync . s3://$S3_BUCKET --delete --exclude ".git/*" --acl public-read
          '''
        }
      }
    }
  }

  post {
    success {
      echo "✅ S3 sync completed successfully"
    }
    failure {
      echo "❌ Build or S3 sync failed"
    }
  }
}
