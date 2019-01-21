# Repo.stream

Say you want to query out and process a bunch of users (like, ten million of them). You can lazily process them straight out of the database using [Repo.stream/2](https://hexdocs.pm/ecto/Ecto.Repo.html#c:stream/2).

Repo.stream/2 breaks your into a bunch of queries that yield a fractional batch of that ten million. You can configure the size of that fraction using the `:max_rows` option.

Then you're free to do your processing in chunks, rather than loading all of those users into memory at one time.
