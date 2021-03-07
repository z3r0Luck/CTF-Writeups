# baby bonechewercon (Web)

![date](https://img.shields.io/badge/date-05%2F03%2F2021-brightgreen)  
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)  
![blockchain category](https://img.shields.io/badge/difficulty-medium-yellow)
![hard dificulty](https://img.shields.io/badge/category-web-blue)

## Flag
``
HTB{b3nt_tw1g_t0_my_will!}
``

## Solution

Starting with reading the description of the challenge it notified us about SSTI feng shui, so it's probably a server side template injection vulnerability

![challinfo](https://github.com/z3r0Luck/CTF-Writeups/blob/master/HTB-UNI%20CTF%20Finals%202021/web_baby_bonechewercon/1.png.png "Challenge Info")

Looking at the files we see PHP is used with Twig, which is a is a template engine for PHP. The web page provide us a form to register a name. Registering a name we get a greeting response with the value we passed to it.

![web1](https://github.com/z3r0Luck/CTF-Writeups/blob/master/HTB-UNI%20CTF%20Finals%202021/web_baby_bonechewercon/2.png "web1")

So we start exploiting this vulnerability with a very simple payload, {{7*7}}, to check if it's going to evaluate so we have the following:
```
http://docker.hackthebox.eu:31119/?name={{7*7}}
```
And we get back 49! So it's vulnerable to SSTI.
![web2](https://github.com/z3r0Luck/CTF-Writeups/blob/master/HTB-UNI%20CTF%20Finals%202021/web_baby_bonechewercon/3.png "web2")

Now we can get remote code execution and list the root folder with the following payload:

```
{{['ls /']|filter('system')}}
```

![web5](https://github.com/z3r0Luck/CTF-Writeups/blob/master/HTB-UNI%20CTF%20Finals%202021/web_baby_bonechewercon/5.png "web5")

We see the flag file there so we just need to read it:
```
{{['cat /flag']|filter('system')}}
```
![web6](https://github.com/z3r0Luck/CTF-Writeups/blob/master/HTB-UNI%20CTF%20Finals%202021/web_baby_bonechewercon/6.png "web6")
