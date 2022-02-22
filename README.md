# Cheatsheet for AWS S3 & RDS in Google Colab

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

## Authentification using csv credentials from AWS
```
creds = pd.read_csv('content/drive/MyDrive/AWS/accessKeys.csv')
aws_access_key_id = creds['Access key ID'][0]
aws_secret_access_key = creds['Secret access key'][0]

session = boto3.Session(aws_access_key_id=aws_access_key_id, 
                        aws_secret_access_key=aws_secret_access_key)
```

## Session avec resource
```
s3 = session.resource("s3")
```

## Session with client
```
s3 = boto3.client('s3')
```

## Create bucket
```
s3.create_bucket(Bucket="currentbucket",CreateBucketConfiguration={'LocationConstraint': "eu-west-1"})
```

## Displaying all buckets
```
[bucket.name for bucket in s3.buckets.all()]
  #equivalent
[bucket['Name'] for bucket in s3.list_buckets()['Buckets'] ]
```

## Listing files in a bucket with client session
```
!aws s3 ls s3://video2text --recursive --human-readable --summarize
```

## Sending csv file to aws
```
s3.meta.client.upload_file('/content/drive/MyDrive/folder/file.csv', 'currentbucket', 'final.csv')
#equiv
s3.Bucket('currentbucket').upload_file('/content/drive/MyDrive/folder/file.csv', Key = 'final.csv')
```

## Checking upload
```
bucket = s3.Bucket("bookingdatacaro")
all_files = [obj.key for obj in bucket.objects.all()]
all_files
```

## Simply read the file
```
import io
obj = s3.Object('currentbucket', 'final.csv')
df = pd.read_csv(io.BytesIO(obj.get()['Body'].read()))
```

## Download file in a given bucket
```
s3.download_file('currentbucket', 'cities.csv', 'cities.csv')
#equiv
!aws s3 cp s3://{'currentbucket'} ./{'/content/'} --recursive 
# renvoie download: s3://currentbucket/final.csv to content/final.csv 
```

# Sending file to RDS
```
!pip install psycopg2-binary
```

## credentials
```
user      = open('/content/drive/MyDrive/folder/user').read()
password  = open('/content/drive/MyDrive/folder/password').read()
db        = open('/content/drive/MyDrive/folder/db').read()
connection_string = 'postgresql+psycopg2://' + user + ':' + password + '@' + db + '/postgres'

from sqlalchemy import create_engine
engine = create_engine(connection_string, echo=True)
df.to_sql('table_name',
          con=engine, 
          if_exists='replace')
 ```

## Reading the file in pandas again ;-)
```
from sqlalchemy.sql import text
sql = text(" SELECT * FROM table_name ")

pd.read_sql(sql, con=engine)
```
