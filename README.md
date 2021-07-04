# Server-Access
How to create SSH key pair and how to access CS servers.

## Table of Contents  
- [Create SSH Private/Public Key Pair](#create-ssh-privatepublic-key-pair)  
- [Connect to Server](#connect-to-server)
- [Setup Keyless Access on bolt](#setup-keyless-access-on-bolt) 
- [Configure SSH Tunnel](#configure-ssh-tunnel) 
- [Configure SSH Tunnel on Windows](#configure-ssh-tunnel-on-windows) 
- [Python Versions](#python-versions) 
- [Install Python Package](#install-python-package) 


## Create SSH Private/Public Key Pair
From any Unix-like system (Linux, Mac OS, or from `bolt.cs.ucr.edu`, etc.), type the following command:
```bash
ssh-keygen -t rsa
```

If you do not want to use a passphrase every time you SSH to a server, just press `Enter` 3 times.

For more detail about SSH key pair, see [How to use ssh-keygen to generate a new SSH key](https://www.ssh.com/ssh/keygen/)

If you have Windows only, you can generate SSH key pair via PuTTY. However, you need to convert your public and private keys into OpenSSH format in order to access the servers. For more details, see [Generate SSH keys using PuTTY](https://www.siteground.com/kb/how_to_generate_an_ssh_key_on_windows_using_putty/) and [Convert PuTTY SSH keys to OpenSSH](https://stackoverflow.com/questions/2224066/how-to-convert-ssh-keypairs-generated-using-puttygen-windows-into-key-pairs-us).


## Connect to Server
Use the following command to SSH to a server:
```bash
ssh -i PATH_TO_PRIVATE_KEY UCR_ID@HOST.cs.ucr.edu
```

If your private key is `~/.ssh/id_rsa`, you can simply use:
```bash
ssh UCR_ID@HOST.cs.ucr.edu
```

## Setup Keyless Access on bolt
Connect to `bolt.cs.ucr.edu` first. Then create a folder `.ssh` under your home folder (`~/.ssh`), set its permission to `0700`. Inside it, create a text file `authorized_keys`, copy the content of your public key (must be in OpenSSH format) to this file as a new line. Set the file permission be `0600`.

Now you can SSH to `bolt.cs.ucr.edu` using your key pair.

A simpler way is to run the following command if you are using Linux or MacOS:
```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub UCR_ID@bolt.cs.ucr.edu
```

## Configure SSH Tunnel
You may only access some servers within the department's network. If you want to access these servers from anywhere like home, you must configure SSH Tunnel to access these servers via a gateway server that is open to the public. You can use `bolt.cs.ucr.edu` for SSH tunnel so you can use one `ssh` command to connect to the server, or you can SSH to bolt first, then SSH to the server from bolt (2 separate `ssh` commands).

1. Create a folder `.ssh` under your home folder (`~/.ssh`), set its permission to `0700`.
2. Create a file `config` under `.ssh`,  copy the following content to it (replace _HOST_ with the server name, change _IdentityFile_ to your private key path if it's not in the default location), set its file permission to `0600`.

```
Host bolt
	HostName bolt.cs.ucr.edu
	User UCR_ID
	IdentityFile ~/.ssh/id_rsa
Host HOST
	Hostname HOST.cs.ucr.edu
	User UCR_ID
	ForwardAgent yes
	ProxyCommand ssh -W %h:%p UCR_ID@bolt
	IdentityFile ~/.ssh/id_rsa
```

Now on your local machine, you can SSH to bolt via `ssh bolt`, or SSH to server HOST via `SSH HOST`. For file transfer, just replace the command `ssh` by `scp`.

## Configure SSH Tunnel on Windows
I personally prefer [WinSCP](https://winscp.net) and [PuTTY](https://www.putty.org/) for both file transfer and command line.

To setup keyless access, from the site page in WinSCP, click `Advanced...`, select the private key via `SSH`&rarr;`Authentication`&rarr;`Private key file:`. Leave the site's password empty. See [Set up SSH public key authentication](https://winscp.net/eng/docs/guide_public_key) for more details.

To setup SSH tunnel, from the site page in WinSCP, click `Advanced...`, select `Connection`&rarr;`Tunnel`, check `Connect through SSH tunnel`, fill `Host name:` with `bolt.cs.ucr.edu`, and fill your user name, also select the same private key file under `Private key file:`. See [Connection Tunneling](https://winscp.net/eng/docs/tunneling) for more details.

## Python Versions
Most servers are running a relative old system that may not have Python installed or an old version of Python like 2.6 or 3.4. I have installed Python 2.7, 3.6, 3.7, 3.8 and 3.9 on some of the servers. You can use `python2.7 -V`, `python3.6 -V`, `python3.7 -V`, `python3.8 -V` and `python3.9 -V` to check if they are installed. It will print the current Python version if it is installed, otherwise it will give you *command not found* error.

If you need some specific version of Python or your package requires a newer version of Python, contact us so we can help.

Current versions are (installed at */usr/local/opt/python/*): Python **2.7.14**, Python **3.6.14**, Python **3.7.11**, Python **3.8.11**, Python **3.9.6**
Plus (installed at */usr/local/opt/*): curl **7.77.0**, Git **2.32.0**, OpenSSL **1.1.1k**, SQLite **3.36.0**.

## Install Python Package
Since in general you won't be given sudo privilege, you cannot install any package system-widely. If you need to install some packages, please use the virtual environment to do so. To use virtual environment to install Python packages, please follow the following steps:

1. Execute `python3.9 -m venv ~/py39venv` to create a virtual environment, assuming you want to use Python 3.9, have your virtual environment stored at `~/py39venv`. Replace `python3.9` with `python3.6` or `python3.7` or `python3.8` if you want to use older versions of Python.
2. Execute `source ~/py39venv/bin/activate` to activate the virtual environment in your current shell.
3. Execute `pip install package1 package2` to install package1 and package2. You can install multiple Python packages at once. If you need to upgrade some installed packages, execute `pip install --upgrade package1 package2`.
4. Execute `python path_to_your_script.py` to run your Python script(s).
5. Execute `deactivate` to deactivate the virtual environment from your shell.

The next time you want to use the same virtual environment to run some scripts, just follow the steps 2, 4 and 5. Or, you can directly call `~/py39venv/bin/python3 path_to_your_script.py` to run your script with any packages installed in your virtual environment. It is the same if you want to run your script in **crontab**:
```
0    *    *    *    *    /home/your_user_name/py39venv/bin/python path_to_your_script.py
```

