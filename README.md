# ntfy
[https://ntfy.sh/](https://docs.ntfy.sh/config/)
<img width="1503" height="1017" alt="ntfy" src="https://github.com/user-attachments/assets/6b656a1b-acd7-4a6c-95ef-d283494cb768" />

## hermes
<img width="1740" height="1110" alt="b482d2e3d431e83d0d2fdea2ca803334" src="https://github.com/user-attachments/assets/2081579d-f8f0-4809-adfc-024f643fee11" />

```
NTFY_TOPIC=hermes-demo-2026060091100
NTFY_ALLOWED_USERS=hermes-demo-2026060091100
NTFY_HOME_CHANNEL=hermes-demo-2026060091100
NTFY_SERVER_URL=https://ntfy.feg.cn


NTFY_TOPIC=abc
NTFY_SERVER_URL=https://msn.feg.cn
NTFY_TOKEN=tk_abc
```

## nginx
```
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

## websocket
```

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

## json stream
```
const token = "tk_abc";

async function listen() {
    const response = await fetch(
        "https://msn.feg.cn/abc/json",
        {
            headers: {
                Authorization: `Bearer ${token}`
            }
        }
    );

    if (!response.ok) {
        console.error("HTTP Error:", response.status);
        return;
    }

    const reader = response.body.getReader();
    const decoder = new TextDecoder();

    let buffer = "";

    while (true) {
        const { value, done } = await reader.read();

        if (done) {
            console.log("Connection closed");
            break;
        }

        buffer += decoder.decode(value, { stream: true });

        let lines = buffer.split("\n");
        buffer = lines.pop();

        for (const line of lines) {
            if (!line.trim()) {
                continue;
            }

            try {
                const msg = JSON.parse(line);

                console.log("Received:");
                console.log(msg);

                // Example:
                // console.log(msg.message);

            } catch (e) {
                console.error("Invalid JSON:", line);
            }
        }
    }
}

async function start() {
    while (true) {
        try {
            await listen();
        } catch (e) {
            console.error("Connection error:", e);
        }

        console.log("Reconnect in 3 seconds...");
        await new Promise(r => setTimeout(r, 3000));
    }
}

start();
```
