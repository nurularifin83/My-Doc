1. Download golang
2. In Terminal directory Download:  
```
a. sudo rm -rf /usr/local/go
b. sudo tar -C /usr/local -xzf go1.23.3.linux-amd64.tar.gz
```
3. Come to home directory ```cd```
4. type ```ls -a```
5. then ```nano .profile```
6. After that paste the following code:
```
export PATH="$PATH:/usr/local/go/bin"
```
then ```Ctrl+0 + Ctrl+X```

7. Done
8. Verify that you've installed go ```go version```
