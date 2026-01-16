# Linux Oneliners

## Python

### HTTP Servers

- **cmd**: `python -m http.server 8080 --bind 127.0.0.1`
- **usage**: Create a simple HTTP file server on 127.0.0.1:8080
- **⚠️warning**: Don't use in sensitive folders or without binding to localhost

### Network Testing

#### **TCP connectivity test:**
- **cmd**: `python -c "import socket; print(not socket.socket().connect_ex(('127.0.0.1',22)))"`
- **usage**: Check if a TCP port is open (example: SSH on localhost)

#### **One-shot TCP "alive" server:**
- **cmd**: `python -c "import socket,sys; p=int(sys.argv[1]) if len(sys.argv)>1 else 8888; s=socket.socket(); s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1); s.bind(('0.0.0.0',p)); s.listen(1); print(f'Listening on port {p}...'); c,a=s.accept(); print(f'{a[0]}:{a[1]} connected'); c.send(b'HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\nConnection: close\r\n\r\nalive\n'); c.close()" 8888`
- **usage**: TCP connectivity tester - responds "alive" once and closes. Test with: `curl http://<ip>:8888`

#### **One-shot UDP "alive" server:**
- **cmd**: `python -c "import socket,sys; p=int(sys.argv[1]) if len(sys.argv)>1 else 8888; s=socket.socket(socket.AF_INET, socket.SOCK_DGRAM); s.bind(('0.0.0.0',p)); print(f'Listening on UDP port {p}...'); data,addr=s.recvfrom(1024); print(f'{addr[0]}:{addr[1]} sent: {data.decode()}'); s.sendto(b'alive\n', addr); s.close(); print('Sent alive and closed')" 8888`
- **usage**: UDP connectivity tester - responds "alive" to any packet. Test with: [UDP client](#udp-client) 


#### **UDP client:**
- **cmd**: `python -c "import socket; s=socket.socket(socket.AF_INET, socket.SOCK_DGRAM); s.sendto(b'ping', ('<server_ip>', 8888)); print(s.recvfrom(1024)[0].decode())"`
- **usage**: Send UDP packet and receive response

### File Transfer

**HTTP file server with checksums:**
- **cmd**: `python -c "import socket,sys,os,hashlib; f=sys.argv[1]; p=int(sys.argv[2]) if len(sys.argv)>2 else 8888; data=open(f,'rb').read(); md5=hashlib.md5(data).hexdigest(); sha=hashlib.sha256(data).hexdigest(); s=socket.socket(); s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1); s.bind(('0.0.0.0',p)); s.listen(1); print(f'File: {f} ({len(data)} bytes)\\nMD5: {md5}\\nSHA256: {sha}\\nURL: http://0.0.0.0:{p}/download\\n'); c,a=s.accept(); req=c.recv(4096).decode(); path=req.split()[1] if len(req.split())>1 else '/'; print(f'{a[0]} -> {path}'); (c.send(f'HTTP/1.1 200 OK\\r\\nContent-Disposition: attachment; filename=\"{os.path.basename(f)}\"\\r\\nContent-Length: {len(data)}\\r\\n\\r\\n'.encode()) and c.sendall(data)) if path=='/download' else c.send(b'HTTP/1.1 404\\r\\n\\r\\n404 - Use /download'); c.close()" file.zip 8888`
- **usage**: Serve a file on `/download` with MD5/SHA256 displayed. Download with: `curl http://<ip>:8888/download -o file.zip`

### Encoding & Decoding

**Base64 encode:**
- **cmd**: `python -c "import base64; print(base64.b64encode(open('file.bin','rb').read()).decode())"`
- **usage**: Encode a binary file to base64 string

**Base64 decode:**
- **cmd**: `python -c "import base64,sys; open(sys.argv[2],'wb').write(base64.b64decode(open(sys.argv[1]).read()))" input.b64 output.bin`
- **usage**: Decode base64 file to binary (handles newlines automatically)

---

## Perl

**TCP port check:**
- **cmd**: `perl -e 'use IO::Socket; $s = IO::Socket::INET->new("127.0.0.1:22") or die "Port is Closed\n"; print "Port is Open\n";'`
- **usage**: Check if a TCP port is open (example: SSH on localhost)

---

## Bash

### Network

**TCP listener with logging:**
- **cmd**: `socat -v TCP-LISTEN:8000,reuseaddr,fork SYSTEM:"echo 'Port 8000 is open from \$SOCAT_PEERADDR'"`
- **usage**: Create a TCP server that logs all incoming connections

**TLS connection test:**
- **cmd**: `openssl s_client -connect google.com:443 2>/dev/null`
- **usage**: Check TLS/SSL connection and certificate info

### File Transfer

**SCP with jump host:**
- **cmd**: `scp -o ProxyJump=<jump_user>@<jump_host> <local_file> <dest_user>@<dest_host>:<dest_path>`
- **usage**: Copy files through an intermediate SSH jump server
