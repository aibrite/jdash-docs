# JDash Cloud

## What is JDash Cloud?
`JDash Cloud` is a JDash API Provider. At `JDash Cloud` we keep all of your dashboard and dashlet configurations for your users so that you can just write your own dashlets and do nothing more, persisting data is in our hands. It is quick and reliable and easy solution for most of our customers.

## How Can I Use JDash Cloud Services?

To use JDash Cloud <a href="https://app.jdash.io/#!/app/account/register" target="_blank">register as a new user here</a>. After registration you will get an api key and secret. We will use this api key and secret are for authentication purposes for your users.

## Authenticating users

We use Json Web Token(JTW) standard for authenticating your users for our Cloud Services. For detailed information about Json Web Token go to <a href="https://jwt.io" target="_blank"> https://jwt.io</a>.

You need to implement your own JWT signing system. We will verify your signature and validate your authentication. 

(Please see  [NodeJS Development](./nodejs-dev.md) or [.Net Core Development](./net-core-dev.md) for examples.)

For your JWT Payload we need 2 Claims :
     <ul>
        <li>
            Subject (sub)
            <ul>
                Your API Key will go here.
            </ul>
        </li>
        <li>
            Data (data)
            <ul>
                Your extra data will go here. This Data Property Must have a "user" property defines your user issued with this token.
            </ul>
        </li>
    </ul>
With these claims you will also need to sign token with your secret key that you have got from <a href="http://app.jdash.io/#!/app/application/" target="_blank">JDash Cloud Applications Page</a>.

Your JWT Payload Should be like below :
```javascript
                    (JSON Notation) 
    {
        "data": {
            "user": "USERNAME OR ID"
         },
        "sub": "YOUR API KEY"
    }
```

## Integrating JDash To JDash Cloud

<small> Before proceeding here please read [Getting Started](../client/getting-started.md) section first.</small>

Lets say you have successfully created your backend authorization code at uri : "/jdashauth?user=user1".

On jdash.Provider.init function of jdash you will have to make some changes like below

```javascript
     jdash.Provider.init({
            apiKey: 'YOUR API KEY',
            userToken: function(tokenCallback){
                // make a XMLHttpRequest (aka Ajax call)
                // you can use any ajax library ie $.ajax, we are just using basic stuff
                var xhr = new XMLHttpRequest();
                xhr.onreadystatechange = function() {
                    if (xhr.readyState == 4 && xhr.status == 200) {
                        // response JWT text will be passed to the JDash Token Callback
                        tokenCallback(xhr.responseText);
                    }
                }
                xhr.open('GET', '/jdashauth?user=user1', true);
                xhr.send();
            }
        })
```

If the response is a valid JWT, JDash will use this JWT for authentication unless it expires. 

#### Notes :    
    1- JDash Will Only Call userToken function when it needs a new Token (initial token request or renewal of expired JWT).

    2- JDash JWT Must be signed with a symmetric algorithm (Preffered  "HS256" algorithm). 

    3- You can use any other claims instead of Data property and its values.

    4- You can use any headers / any header values such as expiration time. 



