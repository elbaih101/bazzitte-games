 

google-chrome-stable is available on a 3rd Party Repository: Google Chrome (for Stable).

Now that apt-key add is deprecated use this:

    Download Key:
```bash
    wget https://dl-ssl.google.com/linux/linux_signing_key.pub -O /tmp/google.pub
```
    Make a keyring for chrome:
```bash
   sudo gpg --no-default-keyring --keyring /etc/apt/keyrings/google-chrome.gpg --import /tmp/google.pub
```
    Set repository:
```bash
   sudo echo 'deb [arch=amd64 signed-by=/etc/apt/keyrings/google-chrome.gpg] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
```
    Install package:
```bash
    sudo apt-get update 
    sudo apt-get install google-chrome-stable
```
Follow the instruction for installation with apt-key add:

    Add Key:
```bash
    wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```
    Set repository:
```bash
    echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
```
    Install package:
```bash
    sudo apt-get update 
    sudo apt-get install google-chrome-stable
```
