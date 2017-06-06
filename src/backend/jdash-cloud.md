# JDash Cloud

## What is JDash Cloud?
JDash Cloud is an api service which safely keeps dashboard and dashlet data of your users. This allows you, as a developer, to focus on dashlet development and user experience instead of backend configuration and security.

## How Can I Use JDash Cloud Services?
To use JDash Cloud <a href="https://app.jdash.io/#!/app/account/register" target="_blank">register as a new user</a>. After registration you will get an api key and secret. 

``Api key`` uniquely identifies your application. ``secret`` value is used to encrypt data between your application and Jdash Cloud.

## Integrating Jdash Cloud into your application
Jdash uses Json Web Token(JTW) standard for authentication of your application and users. For more information about Json Web Token go to <a href="https://jwt.io" target="_blank">https://jwt.io</a>.

Integrating is pretty easy and just requires of sending ``api key`` and ``user`` values encrypted by using ``secret`. 

A sample JWT Payload should look like:
```javascript        
{
    data: {
        user: "USERNAME OR USERID of your end user"
    },
    sub: "API KEY of your application"
}
```

As a general design pattern;

On server side:

1. Provide an end point for getting JWT token i.e. ``/auth/jwt``.
2. Get the authenticated user of this request using your current authentication mechanism (i.e. cookie).
3. Create a JWT payload and sign this payload with your application's secret value which you got from JDash Cloud.

On client side;

1. Make a request to your JWT end point (i.e. ``/auth/jwt``).
2. Use Jdash provider callback with the data you got from your athentication end point.

### NodeJs implementation


### .Net Core implementation


## Integrating your server application to JDash Cloud

Before proceeding here please read [Getting Started](../client/getting-started.md) section first.</small>

Lets say you have successfully created your backend authorization code at uri : "/jdashController/authorize".

On jdash.Provider.init function of jdash you will have to make some changes like below

```javascript

     jdash.Provider.init({
            apiKey: 'YOUR API KEY',
            userToken: function(tokenCallback){
                // make a XMLHttpRequest (aka Ajax call)
                // you can use any ajax library ie $.ajax, we are just using basic stuff

                jdash.Http.get('jdashController/authorize').then(function(result){
                    // response must be a jwt string that will be filled to the result.data property.
                    // response JWT text will be passed to the JDash Token Callback
                    tokenCallback(result.data);
                });
            }
        })
```

If the response is a valid JWT, JDash will use this JWT for authentication unless it expires. 

## Integration JWT Mechanizm for Backend

Jdash Cloud Supports all backends for authorization, however we are providing node-js and .Net samples here 

### Node-JS JWT Mechanizm

You must first install jsonwebtoken npm package 

    npm install jsonwebtoken --save

This will install jsonwebtoken package for your application.

On your router (express application) you can define your own api path. We simple put sample url "/jdashController/authorize" for the example.
```javascript
    router.get('/jdashController/authorize', function(req,res,next){
        var username = getCurrentUserName();
        var jwt = require('jsonwebtoken');
        jwt.sign({
             data: {
                 user : username /* your active user */
             }
        }, 'YOUR API SECRET', {
            algorithm: 'HS256',
            subject: 'YOUR API KEY',
            expiresIn:  "2h" /* optional but recommended */
        }, (err, token) => {
            if(err){
                next(new Error(err)) 
            }else { 
                res.send(token); 
            }
        });
    });
```

You can find detailed information for jsonwebtoken package <a href="https://github.com/auth0/node-jsonwebtoken" target="_blank">here</a>.


### .Net (C#) JWT Mechanizm 

You can use `Jwt.Net` NuGet package <a href="https://www.nuget.org/packages/jose-jwt/" target="_blank" > here </a> or you can find source code on git <a href="https://github.com/dvsekhvalnov/jose-jwt" target="_blank"> here</a>.  Or you can chose any .Net Libraries listed on <a href="https://jwt.io/#libraries-io">https://jwt.io/#libraries-io</a> 

We will show you how to implement JWT for Jose-JWT Nuget Package.

First install Jose-JWT 

     Install-Package jose-jwt


```csharp

       using Jose;

        
       static void Main(string[] args)
        {
          var token = CreateToken();
          // you can use this token for any client authorization request.
        }

        static string CreateToken()
        { 
            DateTime issued = DateTime.Now;
            DateTime expire = DateTime.Now.AddHours(10);

            var payload = new Dictionary<string, object>()
            {
                { "data" , new { user = "CURRENT USER NAME/ID" } }
                {"sub", "YOUR API KEY"},
                {"iat", ToUnixTime(issued).ToString()},
                {"exp", ToUnixTime(expire).ToString()} // optional(recommended)
            };
            
            string token = JWT.Encode(payload, "YOUR SECRET KEY HERE", JwsAlgorithm.HS256);
            return token;
        }

        /// <remarks>
        /// Take a look at http://stackoverflow.com/a/33113820
        /// </remarks>
        static byte[] Base64UrlDecode(string arg)
        { 
            return Convert.FromBase64String(s); // Standard base64 decoder
        }

        static long ToUnixTime(DateTime dateTime)
        {
            return (int)(dateTime.ToUniversalTime().Subtract(new DateTime(1970, 1, 1))).TotalSeconds;
        }
        
```



#### Notes:
1- JDash will call userToken function when it needs a new token (initial token request or renewal of expired JWT).

2- JDash JWT must be signed with a symmetric algorithm (Preffered  "HS256" algorithm). 

3- You can use any other claims instead of Data property and its values.

4- You can use any headers / any header values such as expiration time. 



