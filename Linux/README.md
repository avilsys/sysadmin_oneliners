# Linux Oneliners

## Python

- **cmd**: `python -m http.server 8080 --bind 127.0.0.1`
- **usage**: Create a http server listening on 127.0.0.1:8080
- **⚠️warning**: Don't use this command in a sensitive folder or without binding to localhost, as it exposes files to the network.
-------------
- **cmd**: `python -c "import socket; print(not socket.socket().connect_ex(('127.0.0.1',22)))"`
- **usage**: Check if SSH port is open on localhost (work only with TCP port)
-------------

## Perl

- **cmd**: `perl -e 'use IO::Socket; $s = IO::Socket::INET->new("127.0.0.1:22") or die "Port is Closed\n"; print "Port is Open\n";'`
- **usage**: Check if SSH port is open on localhost (work only with TCP port)

## Bash

- **cmd**: `socat -v TCP-LISTEN:8000,reuseaddr,fork SYSTEM:"echo 'Port 8000 is open from \$SOCAT_PEERADDR'"`
- **usage**: Create a tcp server on port 8000 and logs all incoming connections
-------------
- **cmd**: `openssl s_client -connect google.com:443 2>/dev/null`
- **usage**: Check the TLS connection to google.com
