## Install Java JDK
> 1. Open your browser and navigate to this [link](https://www.geeksforgeeks.org/how-to-install-jdk-in-linux/) and follow that tutorial
## Install JMeter
> 1. Open your browser and navigate to this [link](https://jmeter.apache.org/download_jmeter.cgi) to download JMeter and choose ```Binaries``` option which is ```.tgz``` file
> 2. Open the Terminal and then type ```cd Downloads/```
> 3. Extract the file with this command ```tar -xvzf apache-jmeter-5.6.3.tgz``` and press Enter
> 4. ```ls``` to look the folder we've extracted ```(optional)```
> 5. Move the folder we've extracted to the ```/opt/``` folder ```sude mv apache-jmeter-5.6.3 /opt/jmeter``` and then press Enter
> 6. Come to ```opt``` folder ```cd /opt/jmeter/``` and then type ```ls``` to look the file of JMeter ```(optional)```
> 7. One more thing is to set environment variable path. Go to home folder ```cd```
> 8. Type ```open .bashrc``` and then press Enter
> 9. Go to very button on those file and type this ```export PATH=$PATH:/opt/jmeter/bin``` after that save the file
> 10. Next we'll reload the file with the following command ```source .bashrc``` and then press enter (make sure to type this in the home folder)
> 11. To launch JMETER just type ```jmeter``` on the terminal
> 12. Done
