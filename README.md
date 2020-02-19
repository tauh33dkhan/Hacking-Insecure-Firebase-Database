# Insecure-Firebase

Add '.json' at the end of firebase database url 

For example: https://insecure-firebase.firebaseio.com/.json

If you see `null` in reponse that means the firebase database is insecure and anyone can read and insert the data in 
the database

## POC
```
curl -X POST https://insecure-firebase.firebaseio.com/testing.json \
-d '{"cat": "meow", "dog": "bowbow"}'
```
Now visit https://insecure-firebase.firebaseio.com/testing.json or https://insecure-firebase.firebaseio.com/.json

and you will see new data is added to database



## firebase rules configuration which leads to this vulnerability

```
{
  /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
  "rules": {
    ".read": true,
    ".write": true
  }
}
```
As you can see in the above configuration both read and write set to true which means anyone can read and write to
this firebase database developer some times use this settings for testing purpose but letter forgets to change this
to only allow app users to read or write data.

## Default(secure) firebase rules configuration 

```
{
  /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
  "rules": {
    ".read": false,
    ".write": false
  }
}
```