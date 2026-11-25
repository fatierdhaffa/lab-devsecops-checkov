terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "ap-southeast-1"
}

# 1. S3 Bucket tanpa enkripsi, versioning, dan memakai ACL publik
resource "aws_s3_bucket" "data" {
  bucket = "my-company-data-bucket"
}

resource "aws_s3_bucket_acl" "data_acl" {
  bucket = aws_s3_bucket.data.id
  acl    = "public-read"
}

# 2. Security Group terbuka ke seluruh internet (0.0.0.0/0)
resource "aws_security_group" "web" {
  name = "web-sg"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# 3. IAM Policy dengan wildcard berbahaya
resource "aws_iam_policy" "admin" {
  name = "full-access"
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["*"]
      Resource = ["*"]
    }]
  })
}

# 4. RDS dengan password hardcoded dan publicly accessible
resource "aws_db_instance" "main" {
  identifier          = "mydb"
  engine              = "mysql"
  engine_version      = "8.0"
  instance_class      = "db.t3.micro"
  allocated_storage   = 20
  username            = "admin"
  password            = "Password123!"
  publicly_accessible = true
  storage_encrypted   = false
  skip_final_snapshot = true
}

# 5. EC2 dengan tidak ada enkripsi EBS & tanpa IMDSv2
resource "aws_instance" "web" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t3.micro"

  root_block_device {
    encrypted = false
  }
}
