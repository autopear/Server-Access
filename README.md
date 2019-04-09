# Server-Access
How to create SSH key pair and how to access CS servers.

## Table of Contents  
- [Create SSH Private/Public Key Pair](#create-ssh-privatepublic-key-pair)  
- [Connect to Server](#connect-to-server)
- [Setup Keyless Access on bolt](#setup-keyless-access-on-bolt) 
- [Configure SSH Tunnel](#configure-ssh-tunnel) 
- [Configure SSH Tunnel on Windows](#configure-ssh-tunnel-on-windows) 


## Create SSH Private/Public Key Pair
From any Unix-like system (Linux, Mac OS, or from `bolt.cs.ucr.edu`, etc.), type the following command:

    ssh-keygen -t rsa

If you do not want to use a passphrase every time you SSH to a server, just press `Enter` 3 times.

For more detail about SSH key pair, see [How to use ssh-keygen to generate a new SSH key](https://www.ssh.com/ssh/keygen/)

If you have Windows only, you can generate SSH key pair via PuTTY. However, you need to convert your public and private keys into OpenSSH format in order to access the servers. For more details, see [Generate SSH keys using PuTTY](https://www.siteground.com/kb/how_to_generate_an_ssh_key_on_windows_using_putty/) and [Convert PuTTY SSH keys to OpenSSH](https://stackoverflow.com/questions/2224066/how-to-convert-ssh-keypairs-generated-using-puttygen-windows-into-key-pairs-us).


## Connect to Server
Use the following command to SSH to a server:

    ssh -i PATH_TO_PRIVATE_KEY UCR_ID@HOST.cs.ucr.edu

If your private key is `~/.ssh/id_rsa`, you can simply use:

    ssh UCR_ID@HOST.cs.ucr.edu

## Setup Keyless Access on bolt
Connect to `bolt.cs.ucr.edu` first. Then create a folder `.ssh` under your home folder (`~/.ssh`), set its permission to `0700`. Inside it, create a text file `authorized_keys`, copy the content of your public key (must be in OpenSSH format) to this file as a new line. Set the file permission be `0600`.

Now you can SSH to `bolt.cs.ucr.edu` using your key pair.


## Configure SSH Tunnel
You may only access some servers within the department's network. If you want to access these servers from anywhere like home, you must configure SSH Tunnel to access these servers via a server that is open to the public. You can use `bolt.cs.ucr.edu` for SSH tunnel, or you can SSH to bolt first, then SSH to the server from bolt.

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

To setup keyless access, from the site page in WinSCP, click `Advanced...`, select the private key via `SSH`->`Authentication`->`Private key file:`. Leave the site's password empty. See [Set up SSH public key authentication](https://winscp.net/eng/docs/guide_public_key) for more details.

To setup SSH tunnel, from the site page in WinSCP, click `Advanced...`, select `Connection`->`Tunnel`, check `Connect through SSH tunnel`, fill `Host name:` by `bolt.cs.ucr.edu`, and fill your user name, also select the same private key file under `Private key file:`. See [Connection Tunneling](https://winscp.net/eng/docs/tunneling) for more details.

