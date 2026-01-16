# Linux Oneliners

## Python

- **cmd**: `python -m http.server 8080 --bind 127.0.0.1`
- **usage**: Create a http server listening on 127.0.0.1:8080
- **⚠️warning**: Don't use this command in a sensitive folder or without binding to localhost, as it exposes files to the network.
-------------
- **cmd**: `python -c "import socket; print(not socket.socket().connect_ex(('127.0.0.1',22)))"`
- **usage**: Check if SSH port is open on localhost (work only with TCP port)
-------------
- **cmd**: `python -c "import socket,sys; p=int(sys.argv[1]) if len(sys.argv)>1 else 8888; s=socket.socket(); s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1); s.bind(('0.0.0.0',p)); s.listen(1); print(f'Listening on port {p}...'); c,a=s.accept(); print(f'{a[0]}:{a[1]} connected'); c.send(b'HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\nConnection: close\r\n\r\nalive\n'); c.close()" 8888`
- **usage**: Create a one-shot HTTP server that responds "alive" and closes (port 8888 in this example, but customizable). Useful for quick TCP connectivity debugging between two machines - server shows client IP and port when connection is established.
-------------
- **cmd**: `python -c "import socket,sys; p=int(sys.argv[1]) if len(sys.argv)>1 else 8888; s=socket.socket(socket.AF_INET, socket.SOCK_DGRAM); s.bind(('0.0.0.0',p)); print(f'Listening on UDP port {p}...'); data,addr=s.recvfrom(1024); print(f'{addr[0]}:{addr[1]} sent: {data.decode()}'); s.sendto(b'alive\n', addr); s.close(); print('Sent alive and closed')" 8888`
- **usage**: Create a one-shot UDP server that responds "alive" to any packet received (port 8888 in this example, but customizable). Useful for quick UDP connectivity debugging between two machines - server shows client IP, port and data received.
-------------
- **cmd**: `python -c "import socket; s=socket.socket(socket.AF_INET, socket.SOCK_DGRAM); s.sendto(b'ping', ('<server_ip>', 8888)); print(s.recvfrom(1024)[0].decode())"`
- **usage**: Python UDP client to test the server
-------------
- **cmd**: `python -c "import base64; print(base64.b64encode(open('file.bin','rb').read()).decode())"`
- **usage**: Encode a binary file to base64 in one line
-------------
- **cmd**: `python -c "import base64,sys; open(sys.argv[2],'wb').write(base64.b64decode(open(sys.argv[1]).read()))" input.b64 output.bin`
- **usage**: Decode base64 file with custom input/output filenames


## Perl

- **cmd**: `perl -e 'use IO::Socket; $s = IO::Socket::INET->new("127.0.0.1:22") or die "Port is Closed\n"; print "Port is Open\n";'`
- **usage**: Check if SSH port is open on localhost (work only with TCP port)

## Bash

- **cmd**: `socat -v TCP-LISTEN:8000,reuseaddr,fork SYSTEM:"echo 'Port 8000 is open from \$SOCAT_PEERADDR'"`
- **usage**: Create a tcp server on port 8000 and logs all incoming connections
-------------
- **cmd**: `openssl s_client -connect google.com:443 2>/dev/null`
- **usage**: Check the TLS connection to google.com
-------------
- **cmd**: `scp -o ProxyJump=<jump_user>@<jump_host> <local_file> <dest_user>@<dest_host>:<dest_path>`
- **usage**: Use SCP with an SSH jump host (ProxyJump) to securely copy a file through an intermediate server.
 
