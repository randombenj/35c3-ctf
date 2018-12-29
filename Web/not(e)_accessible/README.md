# Note(e) accessible

## Challenge

We love notes. They make our lifes more structured and easier to manage! In 2018 everything has to be digital, and that's why we built our very own note-taking system using micro services: Not(e) accessible! For security reasons, we generate a random note ID and password for each note.

Recently, we received a report through our responsible disclosure program which claimed that our access control is bypassable...

```
http://35.207.120.163
```

Difficulti estimate: Easy-Medium


## Solution

From the HTML source code of the provided URL you'll get a hint in a comment to an accessible archive
with the source code.
The source code can be found in `src`.

When looking at the source we notice that the flag we want to capture is accessible from the
backends `/admin` endpoint.
Unfortunately, all backend endpoints are only accessible from localhost and we therefore need the frontend
to access it.

The `frontend/index.php` source looks secure enough.
However, the `frontend/view.php` were the code resides to read a note, the note id provided by the user isn't
validated enough.
This `id` is used directly to check for existence and read a file on the local file system.
In these two cases the `id` is casted to an `int` like the following:

```php
"./pws/" . (int) $id . ".pw"
```

When casting with `(int)` PHP casts everything which is a digit from the beginning of the string.
It doesn't care about anything after the digit in that string.
That's a problem when looking how the note file is read:

```php
echo file_get_contents($BACKEND . "get/" . $id);
```

Having this in mind we can create a note using the provided HTML form.
Remember the `id` and inject it in the `view.php` like this:

```
http://35.207.120.163/view.php?id=3691017393700169359/../../admin&pw=4216455ceebbc3038bd0550c85b6a3bf
```

You'll notice the malicious `id` GET parameter `3691017393700169359/../../admin`.
PHP will evalute the `id` like the following when checking for the file:

```php
"./pws/3691017393700169359.pw"
```

but like this when reading the file:

```php
echo file_get_contents($BACKEND . "get/3691017393700169359/../../admin);
```

... which is a problem because that actually accesses the backends `/admin` endpoint. :tada:
