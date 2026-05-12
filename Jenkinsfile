// Simple Jenkins Pipeline to fetch AWS S3 bucket names
pipeline {
    agent any
    
    parameters {
        string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS Region')
    }
    
    environment {
        // AWS credentials stored in Jenkins
        AWS_CREDENTIALS = credentials('aws-credentials')
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }
        
        stage('Validate AWS Credentials') {
            steps {
                script {
                    echo "Validating AWS credentials..."
                    sh '''
                        # Check if AWS CLI is installed
                        aws --version
                    '''
                }
            }
        }
        
        stage('Fetch S3 Buckets') {
            steps {
                script {
                    echo "Fetching S3 bucket names from AWS..."
                    sh '''
                        # Set AWS credentials
                        export AWS_ACCESS_KEY_ID=${AWS_CREDENTIALS_USR}
                        export AWS_SECRET_ACCESS_KEY=${AWS_CREDENTIALS_PSW}
                        
                        # List all S3 buckets
                        echo "============================================"
                        echo "Available S3 Buckets in AWS Account:"
                        echo "============================================"
                        aws s3 ls --region ${AWS_REGION}
                        
                        # Store bucket names in a variable
                        BUCKETS=$(aws s3 ls --region ${AWS_REGION} | awk '{print $3}')
                        
                        echo ""
                        echo "Bucket Names (one per line):"
                        echo "$BUCKETS"
                        
                        # Count total buckets
                        BUCKET_COUNT=$(echo "$BUCKETS" | wc -l)
                        echo ""
                        echo "Total buckets found: $BUCKET_COUNT"
                    '''
                }
            }
        }
        
        stage('Display Results') {
            steps {
                script {
                    echo "S3 bucket fetching completed successfully!"
                }
            }
        }
    }
    
    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed! Check AWS credentials or permissions."
        }
    }
}
