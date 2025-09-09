# serverless-file-operator
My serverless file operator related to cloud computing
pgsql
python-s3-uploader/
│
├── upload_file/
│   └── handler.py
├── serverless.yml
├── requirements.txt
├── .gitignore
└── README.md


upload file
import json
import boto3
import base64
import os

s3 = boto3.client('s3')
BUCKET = os.environ['UPLOAD_BUCKET']

def upload(event, context):
    try:
        body = json.loads(event['body'])
        filename = body['filename']
        content = base64.b64decode(body['file_content'])

        s3.put_object(Bucket=BUCKET, Key=filename, Body=content)

        file_url = f"https://{BUCKET}.s3.amazonaws.com/{filename}"

        return {
            "statusCode": 200,
            "body": json.dumps({"message": "Upload successful", "url": file_url})
        }

    except Exception as e:
        return {
            "statusCode": 500,
            "body": json.dumps({"error": str(e)})
        }


serverless.yml 
service: python-s3-uploader

provider:
  name: aws
  runtime: python3.10
  region: us-east-1
  environment:
    UPLOAD_BUCKET: python-upload-bucket-${sls:stage}
  iamRoleStatements:
    - Effect: Allow
      Action:
        - s3:PutObject
      Resource: arn:aws:s3:::python-upload-bucket-${sls:stage}/*

functions:
  upload:
    handler: upload_file/handler.upload
    events:
      - http:
          path: upload
          method: post
          cors: true

resources:
  Resources:
    UploadBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: python-upload-bucket-${sls:stage}
        AccessControl: PublicRead


requirement.txt 
boto3

gitignore
__pycache__/
.serverless/
.env

 readme.md


 API usage
 {
  "filename": "hello.txt",
  "file_content": "aGVsbG8gd29ybGQ="  # base64 content
}


response
{
  "message": "Upload successful",
  "url": "https://your-bucket.s3.amazonaws.com/hello.txt"
}


remove deployment 
sls remove


 






