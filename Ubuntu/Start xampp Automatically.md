### HOW TO COPY FILE TO ONOTHER FOLDER DIRECTORY:

***CODE DEFAULT :*** ``` sudo cp /path/to/file /path/to/destination ```
***XAMPLE CODE :*** ``` sudo cp /opt/lampp/lampp /etc/init.d ```


## Want to start Xampp service on startup (AUTO START):
====================================================
1. Create a new file in **(/etc/init.d.) :** ``` sudo nano /etc/init.d/xampp ```
2. Paste this code:

```
#!/bin/sh
### BEGIN INIT INFO
# Provides:          xampp
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start and stop xampp
# Description:       Control the XAMPP services.
### END INIT INFO

case "$1" in
  start)
    echo "Starting XAMPP services..."
    /opt/lampp/lampp start
    ;;
  stop)
    echo "Stopping XAMPP services..."
    /opt/lampp/lampp stop
    ;;
  *)
    echo "Usage: /etc/init.d/xampp {start|stop}"
    exit 1
    ;;
esac

exit 0
```

After that, click CTRL-0 and hit Enter to save, and the last click CTRL+X to exit.

3. After that Change its permissions to make it executable with this command: ``` sudo chmod 755 /etc/init.d/xampp ```
4. Enable the script: Now, we need to tell the system to run this script at startup: ``` sudo update-rc.d xampp defaults ```
5. After that restart your computer and congrast your xampp has automatically run