# JDash Cloud

## What is JDash Cloud?
JDash Cloud is an api service which keeps all of your dashboard and dashlet configurations of your users. This allows you, as a developer, focus on dashlet development and user experience.

## How Can I Use JDash Cloud Services?
To use JDash Cloud <a href="https://app.jdash.io/#!/app/account/register" target="_blank">register as a new user</a>. After registration you will get an api key and test user token. 

Api key uniquely identifies your application. Usertoken is used to authenticate end user. 

## Authenticating users
We use Json Web Token(JTW) standard for authenticating your users for our Jdash Cloud. For more information about Json Web Token go to <a href="https://jwt.io" target="_blank">https://jwt.io</a>.

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

Your JWT Payload should look like:
```javascript        
{
    data: {
        user: "USERNAME OR USERID"
    },
    sub: "YOUR API KEY"
}
```

Please see  [NodeJS Development](./nodejs-dev.md) or [.Net Core Development](./net-core-dev.md) for examples.

## Integrating your server application to JDash Cloud

Before proceeding here please read [Getting Started](../client/getting-started.md) section first.</small>

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

#### Notes:
1- JDash will call userToken function when it needs a new token (initial token request or renewal of expired JWT).

2- JDash JWT must be signed with a symmetric algorithm (Preffered  "HS256" algorithm). 

3- You can use any other claims instead of Data property and its values.

4- You can use any headers / any header values such as expiration time. 



