Simple exmaple how it possible to encrypt and decrypt text file using **openssl**
### Encrypt
```shell
openssl enc -aes-256-cbc -salt -pbkdf2 -in text.txt -out encrypted.txt
```

### Decrypt
```shell
openssl enc -aes-256-cbc -d -salt -pbkdf2 -in encrypted.txt
```

[[linux]]
[[cryptography]]