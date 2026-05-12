// Simple Jenkins Pipeline to fetch AWS S3 bucket names using EC2 IAM Role
pipeline {
    agent any
    
    parameters {
        string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS Region')
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
                    echo "Validating AWS credentials from EC2 IAM Role (ec2-jenkins-s3)..."
                    sh '''
                        # Check if AWS CLI is installed
                        aws --version
                        
                        # Verify IAM role is attached
                        echo "Current AWS Identity:"
                        aws sts get-caller-identity
                    '''
                }
            }
        }
        
        stage('Fetch S3 Buckets') {
            steps {
                script {
                    echo "Fetching S3 bucket names from AWS using IAM Role..."
                    sh '''
                        # List all S3 buckets (credentials automatically from EC2 IAM role)
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
            echo "Pipeline executed successfully using IAM Role 'ec2-jenkins-s3'!"
        }
        failure {
            echo "Pipeline failed! Check if IAM role has S3 permissions."
        }
    }
}
