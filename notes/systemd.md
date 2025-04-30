#### Example
```
[Unit]
Description=Some service
After=network.target

[Service]
ExecStart=/path/to/script.sh
Restart=on-failure
User=user
Group=user
WorkingDirectory=/path/to

[Install]
WantedBy=multi-user.target
```

#### File name
```bash
/etc/systemd/system/some.service
```

#### Enable
```bash
systemctl enable some.service
```

#### Disable
```bash
systemctl disable some.service
```

[[linux]]