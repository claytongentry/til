# What the heck was Gopher?

While "Hypertext Transfer Protocol" eventually won out as the dominant means for moving bytes between web applications, I'd argue its name came second to "Gopher". Indeed, the introduction in the [Gopher RFC](https://tools.ietf.org/search/rfc1436) defines "gopher" as the following:

```
gopher  n.  1. Any of various short tailed, burrowing mammals of the
family Geomyidae, of North America.  2. (Amer. colloq.) Native or
inhabitant of Minnesota: the Gopher State.  3. (Amer. colloq.) One
who runs errands, does odd-jobs, fetches or delivers documents for
office staff.  4. (computer tech.) software following a simple
protocol for burrowing through a TCP/IP internet.
```

Gopher was a predecessor protocol to HTTP invented in the early 90s at the University of Minnesota as a campus-wide information system (CWIS). It was a 1) menu-based, 2) text-oriented protocol that emphasized 3) document hierarchy with 4) a simple interface for fetching and moving among documents, not unlike the way file systems work on most modern personal computers. Phew.

It leveraged a client-server model assuming TCP. The client would send the server a line of text called a selector, which usually included a pathname referencing the desired object. As such, resources were theoretically universally identifiable by any combination of server hostname, port, and selector string. For example, a client might send the selector "Stuff:About us" to rawBits.micro.umn.edu at port 70.

The server would then respond with a block of text terminated by a period on a new line, and it retained no state between transactions.

Gopher also leveraged a strict typing system, which I've copied from the RFC:

```
3.8  Item type characters

   The client software decides what items are available by looking at
   the first character of each line in a directory listing.  Augmenting
   this list can extend the protocol.  A list of defined item-type
   characters follows:

   0   Item is a file
   1   Item is a directory
   2   Item is a CSO phone-book server
   3   Error
   4   Item is a BinHexed Macintosh file.
   5   Item is DOS binary archive of some sort.
       Client must read until the TCP connection closes.  Beware.
   6   Item is a UNIX uuencoded file.
   7   Item is an Index-Search server.
   8   Item points to a text-based telnet session.
   9   Item is a binary file!
       Client must read until the TCP connection closes.  Beware.
   +   Item is a redundant server
   T   Item points to a text-based tn3270 session.
   g   Item is a GIF format graphics file.
   I   Item is some kind of image file.  Client decides how to display.

Characters '0' through 'Z' are reserved.  Local experiments should
use other characters.  Machine-specific extensions are not
encouraged.  Note that for type 5 or type 9 the client must be
prepared to read until the connection closes.  There will be no
period at the end of the file; the contents of these files are binary
and the client must decide what to do with them based perhaps on the
.xxx extension.
```

## Try it yourself
Unfortunately, [most modern browsers don't even support Gopher with an extension](https://en.wikipedia.org/wiki/Gopher_(protocol)#Web_browsers), let alone natively.
However, you can browse Gopher (or maybe "pseudo-Gopher," as it's via HTTP) at http://gopher.quux.org:70/. And curl actually supports Gopher as well: Compare `curl gopher://gopher.quux.org:70/` to `curl http://gopher.quux.org:70/`. Alternately, you can try one of a number of Gopher server packages listed [here](https://en.wikipedia.org/wiki/Gopher_(protocol)#Server_software).
