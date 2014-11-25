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
