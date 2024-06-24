Deny all incoming
```shell
sudo ufw default deny incoming
```

Deny all incoming to specific device
```shell
sudo ufw deny in on ens3 to any
```

Allow specific port on specific device
```shell
sudo ufw allow in on ens3 to any port 80
```

Alle all incoming on specific device
```shell
sudo ufw allow in on wg0
```

Accept and enable rools
```shell
sudo ufw enable
```