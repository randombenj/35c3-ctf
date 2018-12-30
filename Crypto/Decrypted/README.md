# Decrypted

## Challange

**Did you know server-side Wee will supports a variety of crypto operations in the future? How else could Wee ever catch up to other short-named languages like Go or all the Cs? Anyway it's still in testing. If you already want to take it for a spin, try /wee/encryptiontest.**

`http://35.207.189.79/`

> Difficulty Estimate: Medium

Good coders should learn one new language every year.

InfoSec folks are even used to learn one new language for every new problem they face (YMMV).

If you have not picked up a new challenge in 2018, you're in for a treat.

We took the new and upcoming Wee programming language from paperbots.io. Big shout-out to Mario Zechner (@badlogicgames) at this point.

Some cool Projects can be created in Wee, like: this, [this] https://paperbots.io/project.html?id=kpyyrl) and that.

Since we already know Java, though, we ported the server (Server.java and Paperbots.java) to Python (WIP) and constantly add awesome functionality. Get the new open-sourced server at /pyserver/server.py.

Anything unrelated to the new server is left unchanged from commit dd059961cbc2b551f81afce6a6177fcf61133292 at badlogics paperbot github (mirrored up to this commit here).

We even added new features to this better server, like server-side Wee evaluation!

To make server-side Wee the language of the future, we already implemented awesome runtime functions. To make sure our VM is 100% safe and secure, there are also assertion functions in server-side Wee that you don't have to be concerned about.

## Solution

We can see in the [server.py](../server.py) that the secret is encypted with a public key:

```python
@app.route("/wee/encryptiontest", methods=["GET"])
def encryptiontest():
    global encrypted
    if not encrypted:
        wee = """
        # we use weelang to encrypt secrets completely secret
        record pubkey
            n: string
            e: string
        end
        var key = pubkey('951477056381671188036079180681828396446164466568923964269373812360568216940258578681673755725586138473475522188240856850626984093905399964041687626629414562063470963902807801143023140969208234239276778397171817582591827008690056789763534174119863046106813515750863733543758319811194784246845138921495556311458180478538856550842509692686396679117903040148607642710832573838027274004952072516749168425434697690016707327002989407014753735313730653189661541750880855213165937564578292464379167857778759136474173425831340306919705672933486711939333953750637729967455118475408369751602538202818190663939706886093046526104043062374288648189070207772477271879494000411582080352364098957455090381238978031676375437980396931371164061080967754225429135119036489128165414029872153856547376448552882344531325480944511714482341088742350110097372766748364926941000441524157824859511557342673524388056049358362600925172299990719998873868038194555465008036497932945812845340638853399732721987228486858193979073913761760370769609347622795498987306822413134236749607735657967667902966667996797241364688793919066445360547749193845825298342626288990158730149727398354192053692360716383851051271618559075048012800235250387837052573541157845958948856954035758915157871993646182544696043757263004887914724250286341123038686355398997399922927237477691269351791943572679717263938613148630387793458838416117454016370454288153779764863162055098229903413503857354581027436855574871814478747237999617879024407403954905986969721336803258774514397600947175650242674193496614652267158753817350136305620268076457813070726099248681642612063203170442453405051455877524709366973062774037044772079720703743828695351198984334830532193564525916901461725538418714517302390850049543856542699391339075976843028654004552169277571339017161697013373622770115406681080294994790626557117129820457988045974009530185622113951540819939983153190486345031549722007896699102268137425607039925174692583738394816628508716999668221820730737934785438568198334912127263127241407430459511422030656861043544813130287622862247904749760983465608684778389799703770877931875268858524702991767450720773677639856979930404508755100624844341829896497906824520180051038779126563860453039035779455387733056343833776802716194138072528278142786901904343407377649000988142255369860324110311816186668720584468851089864315465497405748709976389375632079690963423708940060402561050963276766635011726613211018206198125893007608417148033891841809', '3')
        fun encrypt(message: string, key: pubkey): string
            return bigModPow(strToBig(message), key.e, key.n)
        end
        fun get_cypher(key: pubkey): string
            var message = '{}'
            return encrypt(message, key)
        end

        alert(get_cypher(key))
        """.format(DECRYPTED)
        encrypted = runwee(wee)
    return jsonify({"enc": encrypted})
```

The cryptography is done by the [weeterpreter.ts](../weeterpreter.ts) using the `bigModPow` function:

```typescript
externals.addFunction(
    "bigModPow",
    [
	{name: "base", type: compiler.StringType},
	{name: "exponent", type: compiler.StringType},
	{name: "modulus", type: compiler.StringType}
    ], compiler.StringType,
    false,
    (base: string, exponent: string, modulus: string) => {
	let b = BigInt(base)
	let e = BigInt(exponent)
	let N = BigInt(modulus)
	return eval('""+((b ** e) % N)') // force js since tsc translates ** to Math.Pow... *rolls eyes*
    }
)
```

So basicaly we calculate the following equation: ![equation](https://latex.codecogs.com/gif.latex?b^{e}\bmod&space;N)

Where:

 - `b` is our secret as a `BigInt`
 - `e` is our exponent prime number **3**
 - `N` is our public key

Because we use for `e` such a small number and for `N` a very big publick key, we can simply ignore the modulo operation
and we basicaly have the equation ![equation](https://latex.codecogs.com/gif.latex?b^{e}) without modulo.

To decrypt the message we simply need to calculate the **cube root** of our encrypted message ![equation](https://latex.codecogs.com/gif.latex?\sqrt[e]{c})

Where:

 - `e` is our exponent (cube root) of **3**
 - `c` is our 'encrypted' text ![equation](https://latex.codecogs.com/gif.latex?b^{e})
