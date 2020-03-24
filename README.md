# Insecure-Firebase

### Allows read or read and write

Add '.json' at the end of firebase database url if you see `null` or data in reponse that means the firebase database is insecure and anyone can read or read and write data in database.

For example: https://insecure-firebase.firebaseio.com/.json returns `null`


#### POC (Insert data)
```
curl -X POST https://insecure-firebase.firebaseio.com/testing.json \
-d '{"cat": "meow", "dog": "bowbow"}'
```
This will create a new data location `/testing` in the database

Now visit https://insecure-firebase.firebaseio.com/testing.json or https://insecure-firebase.firebaseio.com/.json

and you will see new data is added to database



## firebase configuration rules which leads to this vulnerability

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
this firebase database, developer some times use this settings for testing purpose but letter forgets to change this
to only allow app users to read or write data.

### Case 2:

### When the child is specified with no auth

When testing firebase database what i was doing before is adding .json at the end of the firebase database url if it returns `null` or data it means that database is vulnerable but if it returns `permission denied` then it means database is secure.

Then I watched a video shared by @B3nac where he showed that developer can set rules for child nodes also. Like this:

```
{
  /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
  "rules": {
    "Admin": {
    ".read": false,
    ".write": false
    },
    "Users": {
      ".read": true,
      ".write": false
    }
  }
}
```

For the purpose of demonstration i deployed a firebase database with the above rules so if you go to 

`https://in-firebase-683e6.firebaseio.com/.json` 

you will get `permission denied` error but if you go to https://in-firebase-683e6.firebaseio.com/Users.json you will get user data which is exposed due to rule set on `Users` node. So we can bruteforce endpoints to find other vulnerable endpoints.


The I thought that developer can set only write access to the endpoint which means if we go to that endpoint we will get `permission denied` error but if we try to write some data we can. The permission rule at development end will look like this:

`
{
  /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
  "rules": {
    "Logs": {
      ".read": false,
      ".write": true
    }
  }
}
`

Here developer sets the write rule at `Logs` endpoint, So if you go to https://in-firebase-683e6.firebaseio.com/Logs.json you will get `permission denied` error but you can write data to it. 

Example:
```
curl -X POST https://in-firebase-683e6.firebaseio.com/Logs.json -d '{"test": "testing"}'
```

If you run the above command you will get something like this in response `{"name":"-M3B_iyZE1RPDaPNuknX"}` which means write is successfull.


##### I encourage you to setup your own database and test on it before palying with program production database a simple mistake can mess all the data out there.

