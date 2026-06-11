# ntfy
https://ntfy.sh/

```hermes
NTFY_TOPIC=hermes-demo-2026060091100
NTFY_ALLOWED_USERS=hermes-demo-2026060091100
NTFY_HOME_CHANNEL=hermes-demo-2026060091100
NTFY_SERVER_URL=https://ntfy.feg.cn
```

```nginx
server {
    listen 443 ssl http2;
    server_name ntfy.feg.cn;

    ssl_certificate     ssl/feg.cn.pem;
    ssl_certificate_key ssl/feg.cn.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://ntfy:80;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_buffering off;
        proxy_cache off;

        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;

        proxy_set_header Connection "";
    }
}

```

```websocket

socket = new WebSocket(
  `wss://ntfy.feg.cn/hermes-demo-2026060091100/ws`
);

socket.onopen = () => {
  //   0	CONNECTING	Socket has been created. The connection is not yet open.
  //   1	OPEN	The connection is open and ready to communicate.
  //   2	CLOSING	The connection is in the process of closing.
  //   3	CLOSED	The connection is closed or couldn't be opened.
};

socket.onmessage = (event) => {
  let data = JSON.parse(event.data);

  if (data.event == "open") {
    return;
  }

  console.dir(data.event)
  
};

socket.onclose = (e) => {
  //
};

socket.onerror = (err) => {
  // socket.close();
};
```
