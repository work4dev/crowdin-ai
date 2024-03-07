/docs/basic/public-api.md
=================
Casdoor Public API

Casdoor's frontend

Any other customized code from the application side

Casdoor Public API

Casdoor Public API

hsluoyz

This RESTful API is referred to as the <code>Casdoor Public API</code>.

The API can be utilized by the following:

The full reference for the <code>Casdoor Public API</code> can be found on Swagger: <a href="https://door.casdoor.com/swagger"><strong x-id="1">https://door.casdoor.com/swagger</strong></a>.

These Swagger docs are automatically generated using Beego's Bee tool.

Casdoor frontend web UI is a <a href="https://developer.mozilla.org/en-US/docs/Glossary/SPA">SPA (Single-Page Application)</a> developed in React.

The React frontend consumes the Casdoor RESTful API exposed by the Go backend code.

In Another word, with HTTP calls, you can do everything just like how Casdoor web UI itself does.

There's no other limitations.

Casdoor client SDKs (e.g., casdoor-go-sdk)

If you want to generate the Swagger docs by yourself, see: <a href="/docs/developer-guide/swagger/#how-to-generate-the-swagger-file">How to generate the swagger file</a>

How to authenticate with <code>Casdoor Public API</code>

1.

By <code>Access token</code>

We can use the access token granted for an authenticated user to call <code>Casdoor Public API</code> as the user itself.

How to get the access token?

The application can get the access token for the Casdoor user at the end of OAuth login process (aka get the token by code and state).

The permissions for the API calls will be the same as the user.

The below examples shows how to call <code>GetOAuthToken()</code> function in Go via casdoor-go-sdk.

func (c *ApiController) Signin() {
code := c.Input().Get("code")
state := c.Input().Get("state")

    token, err := casdoorsdk.GetOAuthToken(code, state)
    if err != nil {
        c.ResponseError(err.Error())
        return
    }

    claims, err := casdoorsdk.ParseJwtToken(token.AccessToken)
    if err != nil {
        c.ResponseError(err.Error())
        return
    }

    if !claims.IsAdmin {
        claims.Type = "chat-user"
    }

    err = c.addInitialChat(&amp;claims.User)
    if err != nil {
        c.ResponseError(err.Error())
        return
    }

    claims.AccessToken = token.AccessToken
    c.SetSessionClaims(claims)

    c.ResponseOk(claims)
}

All granted access tokens can also be accessed via the web UI by an admin user in the Tokens page.

How to authenticate?

2.

By <code>Client ID</code> and <code>Client secret</code>

How to get the client ID and secret?

How to authenticate?

<a href="https://en.wikipedia.org/wiki/Basic_access_authentication">HTTP Basic Authentication</a>, the HTTP header format is:

Authorization: Basic &lt;The Base64 encoding of client ID and client secret joined by a single colon ":"&gt;

If you are not familiar with the Base64 encoding, you can use a library to do that because <code>HTTP Basic Authentication</code> is a popular standard supported by many places.

3.

By <code>username</code> and <code>password</code>

:::caution

This authentication method is not safe and kept here only for compatibility or demo purposes.

We recommend using the previous two authentication methods instead.

What will happen?

The user credential will be exposed as <code>GET</code> parameters the in the request URL.

Moreover, the user credential will be sniffed in plain text by the network if you are using HTTP instead of HTTPS.

We can use the username and password for a Casdoor user to call <code>Casdoor Public API</code> as the user itself.

The username takes the format of <code>&lt;The user's organization name&gt;/&lt;The user name&gt;</code>.

The permissions for the API calls will be the same as the user.

How to authenticate?

HTTP <code>GET</code> parameter, the URL format is:

/page?username=&lt;The user's organization name&gt;/&lt;The user name&gt;&amp;password=&lt;the user's password&gt;"

For example, visit: <a href="https://door.casdoor.com/tokens" x-nc="1">https://door.casdoor.com/tokens</a> for the demo site.

HTTP <code>GET</code> parameter, the URL format is:

/page?access_token=&lt;The access token&gt;

Demo site example: <code>https://door.casdoor.com/api/get-global-providers?access_token=eyJhbGciOiJSUzI1NiIs</code>

HTTP Bearer token, the HTTP header format is:

Authorization: Bearer &lt;The access token&gt;

The application edit page (e.g., <a href="https://door.casdoor.com/applications/casbin/app-vue-python-example" x-nc="1">https://door.casdoor.com/applications/casbin/app-vue-python-example</a>) will show the client ID and secret for an application.

This authentication is useful when you want to call the API as a "machine", "application" or a "service" instead of a user.

The permissions for the API calls will be the same as the application (aka the admin of the organization).

The below examples shows how to call <code>GetOAuthToken()</code> function in Go via casdoor-go-sdk.

HTTP <code>GET</code> parameter, the URL format is:

/page?clientId=&lt;The client ID&gt;&amp;clientSecret=&lt;the client secret&gt;

Demo site example: <code>https://door.casdoor.com/api/get-global-providers?clientId=294b09fbc17f95daf2fe&amp;clientSecret=dd8982f7046ccba1bbd7851d5c1ece4e52bf039d</code>

Demo site example: <code>https://door.casdoor.com/api/get-global-providers?username=built-in/admin&amp;password=123</code>

:::

https://en.wikipedia.org/wiki/Basic_access_authentication
