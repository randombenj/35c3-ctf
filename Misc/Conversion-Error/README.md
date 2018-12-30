# Converstion Error

## Challange

**We _need_ to make sure strings in Wee are also strings in our runtime. Apparently attackers got around this and actively exploit us! We do not know how. Calling out to haxxor1, brocrowd, kobold.io,...: if anybody can show us how they did it, please, please please submit us the token the VM will produce. We added the function assert_string(str: string) for your convenience. You might get rich - or not. It depends a bit on how we feel like and if you reach our technical support or just 1st level. Anyway: this is a call to arms and a desperate request, that, we think, is usually called Bugs-Bunny-Program... or something? Happy hacking.**

http://35.207.189.79/

> Difficulty estimate: Easy

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

In the `assert_string` function of the wee language we see that the comparison is done like the following:

```typescript
typeof str == "string"
```

Note the double equal and not the type comparison `===`. The only commparison which we can input in the metod is `undefined` because
of typescripts type checking in the parameter for string.

We know that we can execute javascript code via the `eval(expr: str)` function, so we can pass `undefined` as a parameter.
We can run a **wee command** by creationg a post request to the `http://35.207.189.79/wee/run` url with the following data:

```json
{
      "code": "alert(assert_string(eval('undefined')))"
}
```

> **token**: 35C3_WEE_IS_TINY_AND_SO_CONFU5ED

