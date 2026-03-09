# Deploy from GitHub to AWS Cloud in One Click

## Secure Automated App Deployment to S3 using CodePipeline, CloudFront & WAF

This project demonstrates automated deployment of a static website from
GitHub to AWS S3 using CI/CD pipeline with AWS CodePipeline, CodeBuild,
CloudFront, and WAF.

Whenever code is pushed to GitHub, the pipeline automatically builds and
deploys the application to S3 and serves it globally through CloudFront.

------------------------------------------------------------------------

# Project Architecture

Developer → GitHub Repository → AWS CodePipeline → AWS CodeBuild →
Deploy to S3 Bucket → S3 Static Website Hosting → ACM Certificate →
CloudFront Distribution → AWS WAF → End Users

------------------------------------------------------------------------

# Repository Used

Repo URL: https://github.com/KastroVKiran/ECR-ECS-Project.git

------------------------------------------------------------------------

# Step 1 --- Create S3 Bucket for Static Website

1.  Go to AWS Console
2.  Open S3
3.  Click Create Bucket

Configuration:

Bucket Name: your-bucket-name\
Enable ACLs\
Uncheck Block all public access

Click Create Bucket.

Enable Static Website Hosting:

Bucket → Properties → Static Website Hosting

Index document: index.html

------------------------------------------------------------------------

# Bucket Policy

Add the following policy in:

S3 → Bucket → Permissions → Bucket Policy

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "<BucketARN>/*"
    }
  ]
}
```

Replace `<BucketARN>` with your bucket ARN.

------------------------------------------------------------------------

# Step 2 --- buildspec.yml

Replace the repository buildspec.yml with:

``` yaml
version: 0.2

phases:
  install:
    commands:
      - echo "No installation needed for static HTML"

  pre_build:
    commands:
      - echo "Preparing build..."

  build:
    commands:
      - echo "Building static HTML site"
      - mkdir -p output
      - cp -r * output/ || true
      - rm -f output/buildspec.yml

  post_build:
    commands:
      - echo "Build complete - $(ls -la output/)"

artifacts:
  files:
    - '**/*'
  base-directory: output
  discard-paths: no
```

------------------------------------------------------------------------

# Step 3 --- Create GitHub Connection

AWS CodeBuild → Settings → Connections → Create Connection

Provider: GitHub\
Connection Name: kastro-project

Connect to GitHub and install the AWS GitHub App.

------------------------------------------------------------------------

# Step 4 --- Create CodeBuild Project

AWS CodeBuild → Create Project

Project Name: myproject-kastro

Source Provider: GitHub\
Repository: Select your repository

Enable automatic rebuild on code push.

Environment:

Operating System: Amazon Linux\
Environment Type: On-demand

Service Role:

codebuild-myproject-kastro-service-role

Buildspec:

Use buildspec file → buildspec.yml

Artifacts:

No artifacts\
Enable CloudWatch Logs

Create project.

------------------------------------------------------------------------

# Step 5 --- Attach CloudFront Permission

IAM → Roles → codebuild-myproject-kastro-service-role

Attach policy:

CloudFrontFullAccess

------------------------------------------------------------------------

# Step 6 --- Create CodePipeline

AWS CodePipeline → Create Pipeline

Pipeline Name: kastro-pipeline\
Execution Mode: Queued

Source Stage:

Provider: GitHub\
Connection: kastro-project\
Repository: Select repo\
Branch: main

Enable pipeline trigger on push events.

Build Stage:

Provider: AWS CodeBuild\
Project: myproject-kastro

Skip Test Stage.

Deploy Stage:

Provider: Amazon S3\
Select your bucket\
Enable Extract files before deploy

Create pipeline.

------------------------------------------------------------------------

# Step 7 --- Run Pipeline

CodePipeline → Release Change

Pipeline stages:

Source → Build → Deploy

After success:

S3 → Static Website Hosting → Open website endpoint.

------------------------------------------------------------------------

# Step 8 --- Setup CloudFront

AWS CloudFront → Create Distribution

Origin Type: Amazon S3\
Select S3 bucket\
Use S3 Website Endpoint

TLS Certificate:

Create using AWS Certificate Manager (ACM).

Deploy distribution and wait until status shows **Deployed**.

------------------------------------------------------------------------

# Step 9 --- Access Website

Use the CloudFront domain name:

Example: https://dxxxx.cloudfront.net

Website will load securely with HTTPS.

------------------------------------------------------------------------

# Step 10 --- Continuous Deployment

Modify index.html in GitHub and commit.

Pipeline automatically triggers:

GitHub → CodePipeline → CodeBuild → S3 Deployment

Website updates automatically.

------------------------------------------------------------------------

# Step 11 --- CloudFront Cache Invalidation

Update buildspec.yml:

``` yaml
post_build:
  commands:
    - echo "Build complete - $(ls -la output/)"
    - echo "Invalidating CloudFront cache..."
    - aws cloudfront create-invalidation       --distribution-id <DistributionID>       --paths "/*"
```

Replace `<DistributionID>` with your CloudFront distribution ID.

------------------------------------------------------------------------

# Final Architecture

GitHub → CodePipeline → CodeBuild → S3 Static Website → CloudFront CDN →
AWS WAF → Global Users

------------------------------------------------------------------------

# Outcome

✔ Automated CI/CD pipeline\
✔ GitHub push triggers deployment\
✔ Static website hosted on S3\
✔ CloudFront global CDN\
✔ HTTPS with ACM\
✔ Automatic cache invalidation
