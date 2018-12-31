# Challenge Equality Error

## Challenge

At assert_equals(num: number), we've added an assert to make sure our VM properly handles equality. With only a few basic types, it's impossible to mess this one up, so the assertion has never been triggered. In case you do by accident, please report the output.

http://35.207.189.79/

> Difficulty estimate: Medium

--

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

The important code for this challenge from weeterpreter.ts is: 
```
externals.addFunction(
    "assert_equals",
    [{name: "num", type: compiler.NumberType}], compiler.StringType,
    false,
    (num: number) => num === num
        ? "EQUALITY WORKS" : flags.EQUALITY_ERROR
)
```

We're looking for an objects that is not equal to itself to get the flag. Luckily we're using JavaScript.

We can use NaN (not a number). `NaN == NaN` result in `false`.
The method needs a Number as an input. NaN is not a number and therefore we cannot pass it directly. However a calculation does also count as a number.

`0/0` is `NaN` in JavaScript.

A POST request to `http://35.207.189.79/wee/run` with payload `{ "code": "alert(assert_equals(0/0))" }` gives the desired response.

```
{
    "code": "alert(assert_equals(0/0))",
    "result": "35C3_NANNAN_NANNAN_NANNAN_NANNAN_BATM4N\n"
}
```