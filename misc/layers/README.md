# Layers

## Challange

**An engineer added a special kind of token to our server: the LAYERS token is unique and there is no way to ever extract it. This means, if anybody every searches for it on the internet or submits it here, we know we have, like, a mole, or something. Dunno. Well we believe it cannot be extracted - so don't even bother.**

[http://35.207.189.79/](http://35.207.189.79/)

> Difficulty estimate: Hard

Good coders should learn one new language every year.

InfoSec folks are even used to learn one new language for every new problem they face (YMMV).

If you have not picked up a new challenge in 2018, you're in for a treat.

We took the new and upcoming Wee programming language from paperbots.io. Big shout-out to Mario Zechner (@badlogicgames) at this point.

Some cool Projects can be created in Wee, like: this, this and that.

Since we already know Java, though, we ported the server (Server.java and Paperbots.java) to Python (WIP) and constantly add awesome functionality. Get the new open-sourced server at /pyserver/server.py.

Anything unrelated to the new server is left unchanged from commit `dd059961cbc2b551f81afce6a6177fcf61133292` at badlogics paperbot github (mirrored up to this commit here).

We even added new features to this better server, like server-side Wee evaluation!

To make server-side Wee the language of the future, we already implemented awesome runtime functions. To make sure our VM is 100% safe and secure, there are also assertion functions in server-side Wee that you don't have to be concerned about.

## Solution

We know that we can get javascript code execution, because we could download the [weeterpreter.ts](../weeterpreter.ts) file from the server and
there is an `eval` function in the **wee language interpreter**:

```typescript
externals.addFunction(
    "eval",
    [{name: "expr", type: compiler.StringType}], compiler.StringType,
    true,
    (expr: string) => {
	return wee_eval(expr)
    }
)
```

We know that the `wee_eval` basicaly calls a *puppeteer* `page.evaluate` function which executes javascript.
The `pagePromise` however calls the following evaluation: `await page.evaluate(\`store('${flags.LAYERS}')\`)`.

We can run a **wee command** by creationg a post request to the `http://35.207.189.79/wee/run` url with the following data:

```json
{
    "code": "alert(eval('document.body.innerHTML'))"
}
```

This simply reads the whole content of the previously loaded file, which now contains the `LAYERS` flag inserted by the `store`
command in the puppeteer as base64 encoded string.

> **token:** 35C3_HOW_DEEP_THE_RABBIT_HOLE_GO3S
