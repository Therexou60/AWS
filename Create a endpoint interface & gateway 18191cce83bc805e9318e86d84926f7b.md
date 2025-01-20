# Create a endpoint interface & gateway

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image.png)

Faire une route vers l’autre réseau 

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%201.png)

Creation de l’endpoint 

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%202.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%203.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%204.png)

Se connecter à une instance 

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%205.png)

Faire un record dans zone 53

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%206.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%207.png)

Copier les adresses ip de l’endpoint

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%208.png)

Créer une règle 

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%209.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2010.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2011.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2012.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2013.png)

Maintenant on va créer un endpoint gateway 

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2014.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2015.png)

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2016.png)

Aller sur l’autre instance 

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2017.png)

Changer la police pour controler l’accès

![image.png](Create%20a%20endpoint%20interface%20&%20gateway%2018191cce83bc805e9318e86d84926f7b/image%2018.png)

{
"Id": "Policy1631305502445",
"Version": "2012-10-17",
"Statement": [
{
"Sid": "Stmt1631305501021",
"Action": "s3:*",
"Effect": "Allow",
"Resource": [
"arn:aws:s3:::<your-bucket-name>-2",
"arn:aws:s3:::<your-bucket-name>-2/*"
],
"Principal": "*"
}
]
}