pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET = 'imdemox123'
        CLOUDFRONT_DISTRIBUTION_ID = 'E11601XAHJAF0N'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Files') {
            steps {
                sh '''
                    pwd
                    ls -la
                '''
            }
        }

        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'aws-credentials', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws s3 sync . s3://$S3_BUCKET \
                        --delete \
                        --exclude ".git/*" \
                        --exclude "Jenkinsfile"
                    '''
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {
                withAWS(credentials: 'aws-credentials', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws cloudfront create-invalidation \
                        --distribution-id $CLOUDFRONT_DISTRIBUTION_ID \
                        --paths "/*"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Website deployed successfully to S3 and CloudFront cache cleared'
        }

        failure {
            echo 'Pipeline failed'
        }

        always {
            cleanWs()
        }
    }
}
