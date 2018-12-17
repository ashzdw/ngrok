**_This sample usage is from_** https://github.com/whisshe/ngrok-config

### Compile and run
1. Download source code.
   ```linux
   git clone https://github.com/ashzdw/ngrok.git
   cd ngrok
   ```
2. Create certificates.
   ```linux
   NGROK_DOMAIN="abc.com" #Change to your domain that's resolved to your ngrok server.

   openssl genrsa -out base.key 2048
   openssl req -new -x509 -nodes -key base.key -days 10000 -subj "/CN=$NGROK_DOMAIN" -out base.pem
   openssl genrsa -out server.key 2048
   openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
   openssl x509 -req -in server.csr -CA base.pem -CAkey base.key -CAcreateserial -days 10000 -out server.crt

   cp base.pem assets/client/tls/ngrokroot.crt
   ```
3. Compile server and client. The output will be in `bin` direcory. `ngrokd` is server, `ngrok` is client.
   ```linux
   make release-server release-client

   # below is examples for cross compile
   GOOS=darwin GOARCH=386 make release-client
   GOOS=windows GOARCH=amd64 make release-client
   GOOS=linux GOARCH=arm make release-client
   ```
4. Start server
   ```linux
   nohup ./bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="your domain" -httpAddr=":8081" -port="40000:100" &
   ```
5. Client side steps
   1. Create ngrok.cfg that's a yml file.
      ```
      server_addr: ngrok.abc.com:4443
      trust_host_root_certs: false
      tunnels:
       ssh:
        remote_port: 1122
        proto:
         tcp: 22
       shadowsock:
        remote_port: 1088
        proto:
         tcp: 1188
       ftp:
        remote_port: 24
        proto:
         tcp: 24
       http:
        subdomain: gitlab
        proto:
         http: 80
         https: 172.3.2.1:443
      ```
   2. Start client
      ```linux
      ./ngrok -config ngrok.cfg start ssh ftp
      or
      ./ngrok -config ngrok.cfg start-all
      ```
