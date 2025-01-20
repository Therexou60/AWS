# Police pour endpoint pour autoriser ou deny l’acces a des bucket

{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Principal": "*",
"Action": "s3:List*",
"Resource": "arn:aws:s3:::*"
},
{
"Effect": "Allow",
"Principal": "*",
"Action": "s3:*",
"Resource": [
"arn:aws:s3:::<LabBucket>",
"arn:aws:s3:::<LabBucket>/*"
]
},
{
"Effect": "Deny",
"Principal": "*",
"Action": "s3:*",
"Resource": [
"arn:aws:s3:::<LabLoggingBucket>",
"arn:aws:s3:::<LabLoggingBucket>/*"
]
}
]
}