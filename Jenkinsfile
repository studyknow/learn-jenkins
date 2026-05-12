// Jenkins Pipeline to fetch AWS S3 bucket names using Python
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
        
        stage('Setup Python Environment') {
            steps {
                script {
                    echo "Setting up Python environment..."
                    sh '''
                        # Check if Python is installed
                        python3 --version
                        
                        # Install boto3 (AWS SDK for Python)
                        pip3 install boto3 --quiet
                        
                        echo "Python environment ready!"
                    '''
                }
            }
        }
        
        stage('Fetch S3 Buckets with Python') {
            steps {
                script {
                    echo "Fetching S3 bucket names using Python..."
                    sh '''
                        python3 << 'EOF'
import boto3
import sys

# AWS Region
aws_region = '${AWS_REGION}'

try:
    # Create S3 client (automatically uses EC2 IAM role credentials)
    s3_client = boto3.client('s3', region_name=aws_region)
    
    # List all S3 buckets
    response = s3_client.list_buckets()
    
    buckets = response.get('Buckets', [])
    
    print("=" * 50)
    print("Available S3 Buckets in AWS Account:")
    print("=" * 50)
    
    if not buckets:
        print("No S3 buckets found in this AWS account.")
    else:
        for idx, bucket in enumerate(buckets, 1):
            bucket_name = bucket['Name']
            creation_date = bucket['CreationDate']
            print(f"{idx}. {bucket_name} (Created: {creation_date})")
        
        print("\n" + "=" * 50)
        print(f"Total buckets found: {len(buckets)}")
        print("=" * 50)
        
except Exception as e:
    print(f"Error fetching S3 buckets: {str(e)}", file=sys.stderr)
    sys.exit(1)

EOF
                    '''
                }
            }
        }
        
        stage('Display Results') {
            steps {
                script {
                    echo "S3 bucket fetching completed successfully using Python!"
                }
            }
        }
    }
    
    post {
        success {
            echo "✅ Pipeline executed successfully using IAM Role 'ec2-jenkins-s3'!"
        }
        failure {
            echo "❌ Pipeline failed! Check if IAM role has S3 permissions."
        }
    }
}
