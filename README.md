# learn-aws-upload-ova-to-ami
how to upload ova to aws as ami

### Prerequisite
1. Ensure user / role has access to get/put for s3 bucket
2. Ensure user / role has policy VMImportExportRoleForAWSConnector
3. Ensure role as trust policy # optional
```bash
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "vmie.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "sts:ExternalId": "vmimport"
                }
            }
        }
    ]
}
```
```bash
wget https://download.vulnhub.com/mrrobot/mrRobot.ova
aws s3 cp mrRobot.ova s3://my-guardduty-threat-list-bucket-a8467242
```
"S3Key":vms/mrRobot.ova # if organize

"Url": "s3://amzn-s3-demo-import-bucket/vms/mrRobot.ova" # alternate
```bash
      echo '[
        {
          "Description": "mrRobot ova image",
          "Format": "ova",
          "UserBucket": {
            "S3Bucket": "your-bucket-name",
            "S3Key": "mrRobot.ova"
          }
        }
      ]' > /tmp/containers.json
```
containers.json
```bash
[
  {
    "Description": "My OVA image",
    "Format": "ova",
    "UserBucket": {
      "S3Bucket": "your-bucket-name",
      "S3Key": "yourfile.ova"
    }
  }
]
```
### Import an image with a single disk
```bash
aws ec2 import-image --description "My server VM" --disk-containers "file://C:\import\containers.json"
IMPORTTASKID=$(aws ec2 import-image --description "My server VM" --disk-containers "file:///tmp/containers.json" --query ImportTaskId --output text)
```
### Monitor the progress
```bash
aws ec2 describe-import-image-tasks --import-task-ids $IMPORTTASKID
```
### Loop Check status
```bash
while [[ "$(aws ec2 describe-import-image-tasks --import-task-ids $IMPORTTASKID --query 'ImportImageTasks[*].StatusMessage' --output text)" != "completed" ]]; do echo $(aws ec2 describe-import-image-tasks --import-task-ids $IMPORTTASKID --query 'ImportImageTasks[*].StatusMessage' --output text); sleep 10; done; echo "Import completed!"
while [[ -z "$(aws ec2 describe-import-image-tasks --import-task-ids $IMPORTTASKID --query 'ImportImageTasks[*].ImageId' --output text)" ]]; do echo "Waiting for AMI ID..."; sleep 10; done; AMI_ID=$(aws ec2 describe-import-image-tasks --import-task-ids $IMPORTTASKID --query 'ImportImageTasks[*].ImageId' --output text);
echo "AMI ID: $AMI_ID"
REGION=$(aws ec2 describe-availability-zones --output text --query 'AvailabilityZones[0].[RegionName]')
aws ec2 copy-image --source-image-id $AMI_ID --source-region us-east-1 --region $REGION --name mrRobot-${AMI_ID#ami-} --description "Based on the show, Mr. Robot."
aws ec2 create-tags --resources $COPIED_AMI_ID --tags Key=Name,Value="mrRobot"
aws ec2 deregister-image --image-id $AMI_ID
```
### Resource
https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html
