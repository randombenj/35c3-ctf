# Challenge /dev/null

## Challenge

We're not the first but definitely the latest to offer dev-null-as-a-service. Pretty sure we're also the first to offer Wee-piped-to-dev-null-as-a-service[WPtDNaaS]. (We don't pipe anything, but the users don't care). This service is more useful than most blockchains (old joke, we know). Anyway this novel endpoint takes input at /wee/dev/null and returns nothing.

http://35.207.189.79/

Difficulty Estimate: Hard

Good coders should learn one new language every year.

InfoSec folks are even used to learn one new language for every new problem they face (YMMV).

If you have not picked up a new challenge in 2018, you're in for a treat.

We took the new and upcoming Wee programming language from paperbots.io. Big shout-out to Mario Zechner (@badlogicgames) at this point.

Some cool Projects can be created in Wee, like: this, this and that.

Since we already know Java, though, we ported the server (Server.java and Paperbots.java) to Python (WIP) and constantly add awesome functionality. Get the new open-sourced server at /pyserver/server.py.

Anything unrelated to the new server is left unchanged from commit dd059961cbc2b551f81afce6a6177fcf61133292 at badlogics paperbot github (mirrored up to this commit here).

We even added new features to this better server, like server-side Wee evaluation!

To make server-side Wee the language of the future, we already implemented awesome runtime functions. To make sure our VM is 100% safe and secure, there are also assertion functions in server-side Wee that you don't have to be concerned about.


## Solution

The important code for this challenge from server.py is: 
```
@app.route("/wee/dev/null", methods=["POST"])
def dev_null():
    json = request.get_json(force=True)
    wee = json["code"]
    wee = """
    var DEV_NULL: string = '{}'
    {}
    """.format(DEV_NULL, wee)
    _ = runwee(wee)
    return "GONE"
```

There were some issues with the server at some point which cuased the POST request to `http://35.207.189.79/wee/dev/null` to time out. The time out occurred when calling the `runwee()` method and therefore the error message contained the `wee` string. Conveniantely this string contained the flag :) 

Note: The timeout is set to 5 seconds according to the error message (which has not been recorded). Maybe it's possible to send enough commands in the payload to trigger this error..