pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET = 'clahantech2026'
        CLOUDFRONT_DISTRIBUTION_ID = 'E60GJW85YNVGL'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/nexgen369/Frontend_Batch02'
            }
        }

        stage('Verify Files') {
            steps {
                sh 'ls -la'
            }
        }

        stage('Deploy to S3') {
            steps {

                withAWS(credentials: 'aws-credentials', region: 'us-east-1') {

                    sh '''
                    aws s3 sync . s3://$S3_BUCKET --delete \
                    --exclude ".git/*" \
                    --exclude "Jenkinsfile"
                    '''
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {

                withAWS(credentials: 'aws-credentials', region: 'us-east-1') {

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
    }
}
