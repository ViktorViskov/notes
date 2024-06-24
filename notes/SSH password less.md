Generate public key in local pc
```shell
ssh-keygen
```

Copy content from `.ssh/id_rsa`
```shell
cat .ssh/id_rsa
```

Need to create file `.ssh/authorized_keys` and pass file content from `.ssh/id_rsa`
```shell
nano .ssh/authorized_keys
```

[[linux]]
[[ssh]]