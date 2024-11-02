# learn-aws-upload-ova-to-ami
how to upload ova to aws as ami

wget 

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
```bash
aws ec2 import-image --description "My server VM" --disk-containers "file://C:\import\containers.json"
aws ec2 import-image --description "My server disks" --disk-containers "file://C:\import\containers.json" # if multiples
```
### Resource
https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html
