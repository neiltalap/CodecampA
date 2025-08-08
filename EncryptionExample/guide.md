1️⃣ Have or generate an RSA SSH key

2️⃣ Convert public key to PEM
OpenSSL needs a PEM-format key:

```
ssh-keygen -f mykey.pub -e -m PKCS8 > mykey_pub.pem
```

3️⃣ Encrypt the file

```
# Generate random AES key
openssl rand -base64 32 > aes.key

# Encrypt the text file with AES
openssl enc -aes-256-cbc -salt -in message.txt -out message.enc -pass file:./aes.key

# Encrypt the AES key with the RSA public key
openssl pkeyutl -encrypt -pubin -inkey mykey_pub.pem -in aes.key -out aes.key.enc

# Remove the unencrypted AES key
shred -u aes.key
```

Now you have:

message.enc → encrypted message
aes.key.enc → AES key encrypted with RSA

4️⃣ Decrypt the file
First, decrypt the AES key using your private key:

```
openssl pkeyutl -decrypt -inkey mykey -in aes.key.enc -out aes.key
```

Then decrypt the message:

```
openssl enc -d -aes-256-cbc -in message.enc -out message_decrypted.txt -pass file:./aes.key

# Remove the unencrypted AES key
shred -u aes.key
```