# JDash Cloud

## What is JDash Cloud?
JDash Cloud is an api service, which safely keeps dashboard and dashlet data of your users. This allows you, as a developer, to focus on dashlet development and user experience instead of backend configuration and security.

## How Can I Use JDash Cloud Services?
In order to use JDash Cloud, <a href="https://app.jdash.io/#!/app/account/register" target="_blank">register as a new user</a>. After registration, you will automatically be assigned an Api Key and Secret for your first application.

``Api Key`` uniquely identifies your application. The ``secret`` value is used to encrypt data between your application and JDash Cloud.

Please remember that you can always access the Api Key and the Secret of your selected application(s), at the Settings Pane of your User Panel at <a href="https://app.jdash.io" target="_blank">jdash.io </a>.

## Integrating JDash Cloud into Your Application
JDash uses JSON Web Token (JWT) standard for authentication of your application and associated users. 

JSON Web Tokens are an open, industry standard RFC 7519 method for representing claims securely between two parties. For more information about JSON Web Token, visit <a href="https://jwt.io" target="_blank">https://jwt.io</a>.

Integrating is pretty easy and requires only sending ``api key`` and ``user`` values, that are encrypted by the ``secret`. 

A sample JWT Payload should look like:
```javascript        
{
    data: {
        user: "USERNAME OR USERID of your end user"
    },
    sub: "API KEY of your application"
}
```

As a general design pattern,

On server side:

1. Set up an end point for retrieving JWT token on your application, i.e. ``/auth/jwt``.
2. Get the authenticated user of this request using your current authentication mechanism (i.e. cookie).
3. Create a JWT payload and sign this payload with your application's ``secret`` value, which you received from JDash Cloud.
4. Send the response to the client side.

On client side:

1. Make a request to your JWT end point (i.e. ``/auth/jwt``).
2. Use JDash Provider callback with the data you got from your athentication end point.

### NodeJs Implementation

#### Step 1: Install JSON Web Token package via npm
JWT.IO allows you to decode, verify and generate JWT.

```no-highlight
npm install JSONwebtoken --save
```

#### Step 2: Implement JWT end point
Below NodeJS code demonstrates a sample implementation.

```javascript
app.get('/auth/jwt', function (req, res, next) {
    // Get end user from request based on your 
    // application's authentication mechanism
    var enduser = req.user;
    var jwt = require('JSONwebtoken');
    jwt.sign({
        data: {
            user: enduser /* current user of request */
        }
        //}, 'TODO: REPLACE WITH SECRET', {
    }, 'a9f26c2daeb4e0af693eab1c59d5e9b0', {
            algorithm: 'HS256',
            subject: 'TODO: REPLACE WITH APIKEY',
            subject: '4f20f93e-5004-4daf-a629-76af8f4f9b95',
            expiresIn: "2h" /* optional but recommended */
        }, (err, token) => {
            if (err) {
                next(new Error(err))
            } else {
                res.send(token);
            }
        });
});
```
#### Step 3: Use end point on client side
Inside ``jdash.ready`` handler, make a call to the above end point to get a secure JWT Token for your end user.

Note that ``jdash.ready`` is called once all the requirements of the JDash Service are met, such that the scripts are loaded and dom is ready.

```javascript
jdash.ready(function () {
        jdash.Provider.init({
            apiKey: 'API KEY',           
            userToken: function (tokenCallback) {
                // make a XMLHttpRequest (aka Ajax call) to your end point
                // you can use any ajax library ie $.ajax, we are just using axios
                // response must be a jwt string.
                jdash.Http.get('auth/jwt').then(function (result) {
                    // securely set end user
                    tokenCallback(null, result.data);
                }, function (err) {
                    tokenCallback(err);
                });
            }
        })
}
```
Keep in mind that JDash Cloud User Panel also allows you to create a Test User Token.

### Step 4: Download source code
Source code of this guide can be found at this [GitHub Repo](https://github.com/aibrite/jdash-nodejs-cloud-tutorial).


### .Net Core implementation

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
1- JDash will call ``userToken`` function when it needs a new token (as initial token request or for the renewal of expired JWT).

2- JDash JWT must be signed with a symmetric algorithm (Preferably "HS256" algorithm). 

3- You can use any other claims instead of ``Data`` property and its values.

4- You can use any headers / any header values such as expiration time. 



