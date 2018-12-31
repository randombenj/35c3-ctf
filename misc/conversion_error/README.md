# Conversion Error

## Challange

**With assert_string(str: string), we assert that our VM properly handles conversions. So far we never triggered the assertion and are certain it's impossible.**

http://35.207.189.79/

> Difficulty estimate: Medium

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

We know how the number conversion works by looking at the [weeterpreter.ts](../weeterpreter.ts) file:

```typescript
externals.addFunction(
    "assert_conversion",
    [{name: "str", type: compiler.StringType}], compiler.StringType,
    false,
    (str: string) => str.length === +str + "".length || !/^[1-9]+(\.[1-9]+)?$/.test(str)
        ? "Convert to Pastafarianism" : flags.CONVERSION_ERROR
)
```

We can now open a `node` repl and try the tests with strings and see that `123` is false for both operations!


We can run a **wee command** by creationg a post request to the `http://35.207.189.79/wee/run` url with the following data:

```json
{
      "code": "alert(assert_conversion('123')))"
}
```

> **token:** 35C3_HOW_DEEP_THE_RABBIT_HOLE_GO3S
