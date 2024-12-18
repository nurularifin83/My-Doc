## How to uninstall app in ubuntu:
> 1. ``sudo apt-get remove nameOfProgram``. Or if you want to uninstall the program and also associated data: ```sudo apt-get purge nameOfProgram```. That will remove the program. It doesn't always remove the dependencies that were installed to make the program work. To clean up those package you can run this after: ``sudo apt-get autoremove``
