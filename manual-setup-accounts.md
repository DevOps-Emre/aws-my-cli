How to set "manually" multiple AWS accounts ?
1) Get access - key

AWS Console > Identity and Access Management (IAM) > Your Security Credentials > Access Keys

2) Set access - file and content

~/.aws/credentials

[default]
aws_access_key_id={{aws_access_key_id}}
aws_secret_access_key={{aws_secret_access_key}}

[{{profile_name}}]
aws_access_key_id={{aws_access_key_id}}
aws_secret_access_key={{aws_secret_access_key}}
3) Set profile - file and content

~/.aws/config

[default]
region={{region}}
output={{output:"json||text"}}

[profile {{profile_name}}]
region={{region}}
output={{output:"json||text"}}
4) Run - file with params

Install command-line app - and use AWS Command Line it, for example for product AWS EC2

aws ec2 describe-instances -- default

aws ec2 describe-instances --profile {{profile_name}} -- [{{profile_name}}]
