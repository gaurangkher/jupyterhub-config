== Jupyter Hub Configuration ==

Saved to ~/.jupyter

Run as systemd, in a python virtual env
~~~~
[Unit]
Description=Jupyter Lab

[Service]
User=<User>
Type=simple
ExecStart=/usr/bin/bash -c 'source bin/activate && jupyter lab --port 8888 --ip=0.0.0.0 --config=~/.jupyter/jupyter_notebook_config.py --notebook-dir=~/notebooks'

[Install]
WantedBy=default.target

~~~~

Nginx Configs for running as reverse proxy with base_dir /jupyterhub

~~~~
location /jupyterhub {
    proxy_pass http://127.0.0.1:8888;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $host;
    proxy_redirect http:// https://;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
location ~* /jupyterhub/(api/kernels/[^/]+/(channels|iopub|shell|stdin)|terminals/websocket)/? {
    proxy_pass http://127.0.0.1:8888;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    # WebSocket support
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
}
~~~~

== Still in Development ==

