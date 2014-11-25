aliceScriptCache
================

A php class for minimizing / serving CSS/TXT/JS in a way friendly to caching.

MIT License, see bottom of class file

What This Class Does
--------------------

This class is used to JavaScript, CSS, and TEXT content to requesting clients
in a manner that is friendly to long-term caching of the file.

Ordinarily with JavaScript and CSS and plain TEXT files, they are static text
files that can be cached by the requesting client (often a web browser or a
proxy server) which is both beneficial to your web server in reduced HTTP
requests as well as beneficial to the client in reduced page load time.

The downside to caching the content of course is when there actually is a
change to the content that clients need to know about. This can be solved by
including a version number in the filename and always remembering to change the
version number, but there is a better way.

What this class does, it allows you to keep the original file without a version
number but reference the file with a version number in your HTML. The version
number will just be the filesystem modification timestamp in decimal seconds
since UNIX Epoch.

When you allow this class to handle the request for the file, several things
will take place:

* The class will attempt to detect character encoding and convert it to UTF-8
  if it is not already UTF-8.
* When the requested filename has a timestamp in it, it will be minified if the
  file is JavaScript or CSS. If it is text, it will be intelligently word
  wrapped to 80 characters per line of text.
* When the requested filename has a timestamp in it, the proper headers will be
  sent telling the client it may cache the file for a year.
* When the requesting client has sent headers asking if the version of the file
  it already has is current, the class will handle the request and check so it
  can send a 304 Not Modified header if the client has the current version, and
  send the current version of the file if the client has an outdated copy.

Requirements
------------

You need a reasonably current version of PHP. I suspect it will work with all
currently supported versions, but I have only tested with PHP 5.6.x branch.

You will need the JSqueeze class by Nicolas Grekas. That class is what actually
does the JavaScript minification. You can get it from
https://github.com/nicolas-grekas/JSqueeze/blob/master/class/JSqueeze.php

You will need to write a wrapper that intercepts server requests to JS/CSS/TXT
files and uses this class to handle the request. If you are using the Apache
web server, mod_rewrite is good for intercepting the request and sending the
request to a wrapper.

It is recommended that you have the php multi-byte mb* string functions
available to PHP. In most cases they will never be needed, but no PHP install
should be without them anyway, and if you use the class to serve text files,
the odds that you will need them go up.

Usage
-----

The class only has two public functions. The first initiates the class, the
second serves the file.

To initiate the class:

    $foo = new scriptCache($reqname, $scriptdir);

If for some reason you do not want the class to compensate for files that are
not already UTF-8 add a third argument of FALSE

`$reqname` is the name of the script the web page is requesting, e.g.
`foo-73484273.js`

`$scriptdir` is the directory on the server filesystem where `foo.js` resides,
e.g. `/srv/mywebsite/js/`

You must use a trailing slash with the `$scriptdir`