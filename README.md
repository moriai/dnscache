Dnscache
----

Simple DNS proxy supporting one upstream.
Designed for using slow and unreliable upstream DNS servers like Tor's DNS resolver.
Trades consistency for availability. Not for serious use.

License = MIT or Apache 2.0

There are some pre-built versions on Github releases.

---

```
dnscache 0.1.1
Vitaly _Vi Shukela <vi0oss@gmail.com>
Simple DNS cacher.

USAGE:
    dnscache [OPTIONS] <listen_addr> <upstream_addr> <db>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
        --neg-ttl <neg_ttl>    Negative reply TTL, seconds [default: 30]

ARGS:
    <listen_addr>      Listen address and port
    <upstream_addr>    Upstream DNS server address and port
    <db>               Path to LevelDB database directory
```

-----

Features:

* IPv6 AAAA records
* Forwarding of trickier queries as is
* Multi-question queries
* Minimal protection from poisoning by filtering domain names in replies
* Always tries to immediately return some A or AAAA records for client to try, no waiting for refreshing.

Notes:

* It does not construct DNS requests on its own, it reuses client-constructed packets
* Uncached queries (non-A, non-AAAA or non-IN) are forwarded based in ID
* TTL may be 0 in replies
* Single threaded, single UDP socket
* If all A or AAAA entries disappear in reply, cached ones retain instead. AAAA resolution sometimes works in Tor DNS resolver, sometimes not.

Concerns:

* CNAME not handled at all yet
* ID field is handled inconsistently
* Entries are never deleted from cache
* If data is stale, it first replies with TTL 0, then re-checks in upstream
* The used LevelDB implementation is not recommended for serious use yet.
* The same socket used both for client and for upstream communication. Can't listen only on 127.0.0.1, but rely on 8.8.8.8.
