# SSH

```javascript
// generate key
ssh-keygen -t rsa -b 4096 -C "email@email.com"

// starting ssh agent
eval $(ssh-agent -s)

// add ssh key
ssh-add ~/.ssh/id_rsa

// copy public key either by opening the rsa_pub file
```

## Allowing SSH on Linux

```
sudo mkdir ~/.ssh
sudo chmod 700 ~/.ssh
sudo nano ~/.ssh/authorized_keys
sudo chown username:username ~/.ssh -R
sudo chmod 600 ~/.ssh/authorized_keys
sudo service sshd restart
```



## Also See

* [Multiple SSH Keys settings for different github account](https://gist.github.com/jexchan/2351996)

