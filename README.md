# How to Debug Cookies

## How to Evaluate if it's Working

After you login (or do whatever request returns a cookie), see if the cookie is being stored in your browser by opening Chrome devtools and going to the Application tab. Then click cookies on the left and select your domain:

![devtools](https://github.com/benawad/how-to-debug-cookies/raw/master/Screen%20Shot%202019-06-19%20at%206.44.47%20PM.png)

If a cookie doesn't show up for you, use Firefox devtools to see if the login request sent back a `set-cookie` header:

![firefox devtools](https://github.com/benawad/how-to-debug-cookies/raw/master/Screen%20Shot%202019-06-19%20at%207.56.43%20PM.png)

## Make sure your Configuration is Correct

### credentials

Make sure you set `credentials` to `include` when you make a request to the server.

For Apollo:

```js
const httpLink = createHttpLink({
  uri: config.serverUrl,
  credentials: "include"
});
```


Make sure you set `credentials` to `true` in your cors settings.

```js
app.use(
  cors({
    credentials: true,
    origin: process.env.ORIGIN
  })
);
```

### proxy

If your server is behind a proxy (Heroku, Nginx, Now, etc...) add the following on your server:

```js
app.set("trust proxy", 1);
```

### secure

When creating a cookie, only set `secure` to `true` if your using `https`

```js
res.cookie("qid", token, {
  path: "/",
  httpOnly: true,
  secure: false,
  maxAge: 1000 * 60 * 60 * 24 * 7 // 7 days
});
```

### 3rd party cookies

Sometimes the browser will eat your cookie if it thinks it's a 3rd party one. You can avoid this by using a custom domain and having your backend and frontend on different subdomains. For example, have the website on `www.example.com` and the server on `api.example.com`.

### domain

If you refresh your browser and the cookie disapears this fix is for you.

You'll need a custom domain and when you create your cookie set that as the domain with a period in front:

```js
res.cookie("qid", token, {
  path: "/",
  httpOnly: true,
  secure: false,
  domain: '.example.com',
  maxAge: 1000 * 60 * 60 * 24 * 7 // 7 days
});
```

Also make sure if your doing SSR that you forward the cookie header when you SSR requests.
