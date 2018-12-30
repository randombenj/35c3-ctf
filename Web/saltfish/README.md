# Challenge saltfish

62 Points

## Challenge

"I have been told that the best crackers in the world can do this under 60 minutes but unfortunately I need someone who can do this under 60 seconds." - Gabriel

http://35.207.89.211

## Solution

At the given IP there is the following code available:

```
<?php
  require_once('flag.php');
  if ($_ = @$_GET['pass']) {
    $ua = $_SERVER['HTTP_USER_AGENT'];
    if (md5($_) + $_[0] == md5($ua)) {
      if ($_[0] == md5($_[0] . $flag)[0]) {
        echo $flag;
      }
    }
  } else {
    highlight_file(__FILE__);
  }

```

It seems like there are 2 input fields, the get parameter "pass" and the user-agent. The parameters must met some restrictions to get our flag.

The first condition checks if the md5 hash of $_ (which is the "pass" get parameter) plus the first character of $_ is equal to the md5 hash of the user agent. There are some strange behaviour of php if letters are added together, so if the first letter of $_ is a letter (or 0) and the user agent is the same value as the "pass" parameter, the first condition gets passed.

To pass the second condition, only the first character of the pass param gets compared with the md5 hash of the first character and the flag appended. So there are only a few possible input values for the second condition (all ASCII characters).

So lets brute force all possible inputs (loop all ASCII characters and numbers). Both inputs must be the same value, so this will be quick. After the second guess there it is:

```
root@blackbox:# curl -A "b" "http://35.207.89.211/?pass=b"
<br />
<b>Warning</b>:  A non-numeric value encountered in <b>/var/www/html/index.php</b> on line <b>5</b><br />
35c3_password_saltf1sh_30_seconds_max
```

