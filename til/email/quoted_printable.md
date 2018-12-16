# Quoted-Printable Encoding

QP encoding is a type of character encoding that lets you send 8-bit data to (older) systems built for 7-bit data.

As I learned building a newsletter delivery service, it's a very common encoding for SMTP message bodies. For example, some mail clients enforce line-length limits on the SMTP requests you send them. QP encoding takes care of this by limiting line length to 76 characters and escaping them with an equal sign.

One nice thing about Quoted-Printable encoding is that human-legible data largely remains human-legible after encoding.

It's discussed in greater detail in [RFC 2045](https://www.ietf.org/rfc/rfc2045.txt). Look at this (fun!) example from the RFC:

```
Thus if the "raw" form of the line is a single unencoded line that
says:

  Now's the time for all folk to come to the aid of their country.

This can be represented, in the Quoted-Printable encoding, as:

  Now's the time =
  for all folk to come=
   to the aid of their country.
 ```

But you can also encode more complicated types of content, such as HTML:

```
<html lang="en">
  <head>
    <meta charset="utf-8">
    <link rel="shortcut icon" href="/favicon.png">
    <meta name="viewport" content="width=device-width,initial-scale=1,shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css">
    <link href="https://fonts.googleapis.com/css?family=Oswald:300,700" rel="stylesheet">
    <title>Clayton Gentry</title>
    ...
```

becomes
```
<html lang=3D"en">=0A  <head>=0A    <meta charset=3D"utf-8">=0A    <link re=
l=3D"shortcut icon" href=3D"/favicon.png">=0A    <meta name=3D"viewport" co=
ntent=3D"width=3Ddevice-width,initial-scale=3D1,shrink-to-fit=3Dno">=0A    =
<link rel=3D"stylesheet" href=3D"https://stackpath.bootstrapcdn.com/bootstr=
ap/4.1.3/css/bootstrap.min.css">=0A    <link href=3D"https://fonts.googleap=
is.com/css?family=3DOswald:300,700" rel=3D"stylesheet">=0A    <title>Clayto=
n Gentry</title>
```
