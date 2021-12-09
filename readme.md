s3 bucket using terraform
create a folder local folder to store and manage the file under that folder create a .tf file. Then provide aws provider to access the environment
provider “aws” {
region = “value”
access_key = “value”
secret_key = “value”
}

create a s3 bucket with enabled versioning, lifecycle, Acess control and server-side encrpytion. Like this you can create sameway the other two s3 bucket.
resource “aws_s3_bucket” “b” {
bucket = “crazy-berlin-weather-hourly”
acl = “private”

versioning
versioning {
enabled = true
}

Lifecycle
lifecycle_rule {
id = “log”
enabled = true

prefix = "log/"
tags = {
  rule      = "log"
  autoclean = "true"
}
transition {
  days          = 30
  storage_class = "STANDARD_IA" # or "ONEZONE_IA"
}
transition {
  days          = 60
  storage_class = "GLACIER"
}
expiration {
  days = 90
}
}

lifecycle_rule {
id = “tmp”
prefix = “tmp/“
enabled = true

expiration {
  date = "2016-01-12"
}
}
}

access control
grant {
id = data.aws_canonical_user_id.current_user.id
type = “CanonicalUser”
permissions = [“FULL_CONTROL”]
}

grant {
type = “Group”
permissions = [“READ”, “WRITE”]
uri = “web host”
}
}

server-side encryption
server_side_encryption_configuration {
rule {
apply_server_side_encryption_by_default {
kms_master_key_id = aws_kms_key.mykey.arn
sse_algorithm = “—-“
}
}
tags = {
Name = “My bucket”
Environment = “Dev”
}
}

On every rules we need to use terraform apply to see if this works or not
it will create the bucket with all the policies