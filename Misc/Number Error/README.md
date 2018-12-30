# Number Error

## Challange

**The function assert_number(num: number) is merely a debug function for our Wee VM (WeeEm?). It proves additions always work. Just imagine the things that could go wrong if it wouldn't!**

`http://35.207.189.79/`

> Difficulty estimate: Easy - Medium

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

In the [weeterpreter.ts](../weeterpreter.ts) sourcecode we can see the `assert_number` function implementation:

```typescript
externals.addFunction(
    "assert_number",
    [{name: "num", type: compiler.NumberType}], compiler.StringType,
    false,
    (num: number) => !isFinite(num) || isNaN(num) || num !== num + 1
	? "NUMBERS WORK" : flags.NUMBER_ERROR
)
```

Here we can simply provoke an overflow by passing on a very big number.
We can run a **wee command** by creationg a post request to the `http://35.207.189.79/wee/run` url with the following data:

```json
{
  "code": "alert(assert_number(11111111111111111111111111111111111111111111))"
}
```

> **token:** 35C3_THE_AMOUNT_OF_INPRECISE_EXCEL_SH33TS





