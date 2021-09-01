# apple-ads-python-demo

Apple Ads 广告管理 API 的Python 代码示例，包括 OAuth 实施参考。Apple Ads Campaign Management API, OAuth, Report API examples

## 重点注意

安装三方依赖库

```bash

pip install PyJWT
pip install requests

```

## 邀请 API 用户

![Invite User](assets/invite-api-user.jpg?raw=true "Invite User")

## 上传 Public Key

![Upload a Public Key](assets/upload-public-key.jpg?raw=true "Upload a Public Key")

## 代码

```python

import datetime as dt

import jwt
import requests

client_id = 'SEARCHADS.27478e71-3bb0-1106-998c-ljh182405577'
team_id = 'SEARCHADS.27478e71-3bb0-1106-998c-ljh182405577' 
key_id = 'bacaebda-e219-1106-a907-ljh182405577' 
audience = 'https://appleid.apple.com'
alg = 'ES256'

# Define issue timestamp.
issued_at_timestamp = int(dt.datetime.utcnow().timestamp())
# Define expiration timestamp. May not exceed 180 days from issue timestamp.
expiration_timestamp = issued_at_timestamp + 86400*180 

# Define JWT headers.
headers = dict()
headers['alg'] = alg
headers['kid'] = key_id

# Define JWT payload.
payload = dict()
payload['sub'] = client_id
payload['aud'] = audience
payload['iat'] = issued_at_timestamp
payload['exp'] = expiration_timestamp
payload['iss'] = team_id 

# Path to signed private key.
KEY_FILE = '/path/to/private-key.pem' 

with open(KEY_FILE,'r') as key_file:
     key = ''.join(key_file.readlines())

client_secret = jwt.encode(
payload=payload,  
headers=headers,
algorithm=alg,  
key=key
)

with open('client_secret.txt', 'w') as output: 
     output.write(client_secret.decode("utf-8"))


print("clientSecret 建议保存，有效期可设置最长 180 天");
print(client_secret.decode("utf-8"))

url = "https://appleid.apple.com/auth/oauth2/token"
headers = {
     'Host': 'appleid.apple.com',
     'Content-Type': 'application/x-www-form-urlencoded'
}
data = {
     'grant_type': 'client_credentials',
     'agency': 'ljh',
     'scope': 'searchadsorg',
     'client_id': client_id,
     'client_secret': client_secret
}
resp = requests.post(url, headers=headers, data=data)
result = resp.json()
access_token = result.get('access_token')

print("access_token 有效期1个小时")
print(access_token)
```

