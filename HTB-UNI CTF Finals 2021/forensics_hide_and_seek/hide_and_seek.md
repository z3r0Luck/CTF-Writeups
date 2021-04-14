# HideAndSeek (Forensics)

![date](https://img.shields.io/badge/date-06%2F03%2F2021-brightgreen)  
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)  
![blockchain category](https://img.shields.io/badge/difficulty-medium-yellow)
![hard dificulty](https://img.shields.io/badge/category-Forensics-blue)

## Flag

``
HTB{g0oD_hUnt1n_y0u_f0und_1t!}
``

## Solution

We're given a linux machine with credentials for user and we can get root with sudo. In root home directory there is a solveme executable which checks if we remediated the issues and if all are remediated then we get the flag

So we start searching the machine firstly at the users it has. In /etc/passwd there is user hodor where he has an id 0, where he has root privileges so we have to delete him from passwd and shadow files.

![challinfo](issue2_1.png "Challenge Info")

![challinfo](issue2_2.png "Challenge Info")

Now the issue 2 is fully remediated.

Looking at the authorized_keys at root home directory we see a strange ssh key for a bd user. So we remove it.

![challinfo](issue4_1.png "Challenge Info")

Also we found at /etc/rc2.d/S01ssh symlink of /etc/init.d/ssh which starts/stop the ssh daemon, it's executed at runlevel 2 (i.e every time ssh daemon starts/stop or the machine boot up, more about runlevels [here](https://en.wikipedia.org/wiki/Runlevel)). Inside the S01ssh the attacker insert his ssh key in root authorized_keys, so we remove the command.

![challinfo](issue4_2.png "Challenge Info")
![challinfo](issue4_3.png "Challenge Info")

Now issue 4 is fully remediated.

Looking for unusual cron jobs we find at /etc/cron.daily an unsual access-up script which creates some weird binaries at /sbin/ folder.

![challinfo](issue3_1.png "Challenge Info")
![challinfo](issue3_2.png "Challenge Info")

We delete the access-up script. Then we delete the weird named binaries in /sbin folder.

![challinfo](issue3_3.png "Challenge Info")
![challinfo](issue3_4.png "Challenge Info")

Now the issue 3 is fully remediated.

Finally looking the .bashrc file of user at /home/user/ and at aliases we see the cat has an alias that executing a bash command and the actual cat command.

![challinfo](issue1_1.png "Challenge Info")

And now issue 1 is fully remediated. So running the solveme executable we get flag

![challinfo](flag.png "Challenge Info")