# learn-aws-upload-ova-to-ami
how to upload ova to aws as ami

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
Import an image with a single disk
```bash
aws ec2 import-image --description "My server VM" --disk-containers "file://C:\import\containers.json"
aws ec2 import-image --description "My server VM" --disk-containers "file:///tmp/containers.json"
```
Monitor the progress
```bash
aws ec2 describe-import-image-tasks --import-task-ids "import-ami-1234567890abcdef0>"
```
### Resource
https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html
