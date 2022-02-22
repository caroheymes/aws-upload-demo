# Cheatsheet for aws s3 & RDS

## Authentification using AWS cli
```
!pip install awscli
import awscli

text = '''
[default]
aws_access_key_id = AWS_ACCESS_KEY_ID 
aws_secret_access_key = AWS_SECRET_ACCESS_KEY
region = eu-west-1
'''
path = "/content/drive/MyDrive/config/awscli.ini"
with open(path, 'w') as f:
   f.write(text)
!cat /content/drive/MyDrive/config/awscli.ini


!export AWS_SHARED_CREDENTIALS_FILE=/content/drive/MyDrive/config/awscli.ini
path = "/content/drive/MyDrive/config/awscli.ini"
os.environ['AWS_SHARED_CREDENTIALS_FILE'] = path
print(os.environ['AWS_SHARED_CREDENTIALS_FILE'])
```
