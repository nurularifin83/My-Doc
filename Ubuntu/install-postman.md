# How to Installing Postman on Ubuntu

> 1. If any version of postman is installed we need to remove it
```sudo rm -rf /opt/Postman```

> 2. This will install postman to /tmp directory and move it to /opt/ directory.
```tar -C /tmp/ -xzf <(curl -L https://dl.pstmn.io/download/latest/linux64) && sudo mv /tmp/Postman /opt/```

> 3. Create a desktop file
```
sudo tee -a /usr/share/applications/postman.desktop << END
[Desktop Entry]
Encoding=UTF-8
Name=Postman
Exec=/opt/Postman/Postman
Icon=/opt/Postman/app/resources/app/assets/icon.png
Terminal=false
Type=Application
Categories=Development;
END
```

> 4. Done. You're good to go.