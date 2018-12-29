# Challenge Logged In

47 Points

# Challenge

Phew, we totally did not set up our mail server yet. This is bad news since nobody can get into their accounts at the moment... It'll be in our next sprint. Until then, since you cannot login: enjoy our totally finished software without account.

http://35.207.189.79/

Difficulty Estimate: Easy

Good coders should learn one new language every year.

InfoSec folks are even used to learn one new language for every new problem they face (YMMV).

If you have not picked up a new challenge in 2018, you're in for a treat.

We took the new and upcoming Wee programming language from paperbots.io. Big shout-out to Mario Zechner (@badlogicgames) at this point.

Some cool Projects can be created in Wee, like: this, this and that.

Since we already know Java, though, we ported the server (Server.java and Paperbots.java) to Python (WIP) and constantly add awesome functionality. Get the new open-sourced server at /pyserver/server.py.

Anything unrelated to the new server is left unchanged from commit dd059961cbc2b551f81afce6a6177fcf61133292 at badlogics paperbot github (mirrored up to this commit here).

We even added new features to this better server, like server-side Wee evaluation!

To make server-side Wee the language of the future, we already implemented awesome runtime functions. To make sure our VM is 100% safe and secure, there are also assertion functions in server-side Wee that you don't have to be concerned about.


# Solution

In the server.py we find the following important code for this challenge:

```
@app.route("/api/login", methods=["POST"])
def login():
    print("Logging in?")
    # TODO Send Mail
    json = request.get_json(force=True)
    login = json["email"].strip()
    try:
        userid, name, email = query_db("SELECT id, name, email FROM users WHERE email=? OR name=?", (login, login))
    except Exception as ex:
        raise Exception("UserDoesNotExist")
    return get_code(name)

def get_code(username):
    db = get_db()
    c = db.cursor()
    userId, = query_db("SELECT id FROM users WHERE name=?", username)
    code = random_code()
    c.execute("INSERT INTO userCodes(userId, code) VALUES(?, ?)", (userId, code))
    db.commit()
    # TODO: Send the code as E-Mail instead :)
    return code

@app.route("/api/verify", methods=["POST"])
def verify():
    code = request.get_json(force=True)["code"].strip()
    if not code:
        raise Exception("CouldNotVerifyCode")
    userid, = query_db("SELECT userId FROM userCodes WHERE code=?", code)
    db = get_db()
    c = db.cursor()
    c.execute("DELETE FROM userCodes WHERE userId=?", (userid,))
    token = random_code(32)
    c.execute("INSERT INTO userTokens (userId, token) values(?,?)", (userid, token))
    db.commit()
    name, = query_db("SELECT name FROM users WHERE id=?", (userid,))
    resp = make_response()
    resp.set_cookie("token", token, max_age=2 ** 31 - 1)
    resp.set_cookie("name", name, max_age=2 ** 31 - 1)
    resp.set_cookie("logged_in", LOGGED_IN)
    return resp
```

The username is 'admin' (this was a guess).

The code we need for the login is returned in the post request.

We create a POST request to http://35.207.189.79/api/login with the payload `{ "email": "admin" }`. The response is a verification code (e.g. `vneoud`).

This code can then be used to create a post request on http://35.207.189.79/api/verify with payload `{ "code": "vneoud" }`.

Now a browser cookie is set with the LOGGED_IN flag `35C3_LOG_ME_IN_LIKE_ONE_OF_YOUR_FRENCH_GIRLS`.