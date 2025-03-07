# The .Profile

this file contains the user variables  it can be set using the nano editor or any text editor
then it may be edited in many way for example

1. setting the Variable MANGA

```bash
nano ~/.profile
```

a. MANGA="Zenga"
b. export MANGA="Zenga"

2. after editing the file save and run the following command to read it or it may be readed from the next login
```bash
source ~/.profile
```

# the .bashrc 
similar to the  .profile but it has a higher priority setting it in the .bashrc will have the same effect on the user but in the without login shells as well

```bash
nano ~/.bashrc
```

a. MANGA="Zenga"
b. export MANGA="Zenga"

2. after editing the file save and run the one of the following following commands to read it or it may be readed from the next login
```bash
source ~/.profile
```
```bash
source ~/.bashrc
```

# the system environment 

Variables reflects on all the users on the machiene its under the root user so will have to use sudo 

1. setting the Variable MANGA

```bash
sudo nano /etc/environment
```

a. MANGA="Zenga"
b. export MANGA="Zenga"

2. after editing the file save and run the following command to read it or it may be readed from the next login
```bash
source ~/.profile
```
```bash
source ~/.bashrc
```
```bash
source /etc/environment
```
