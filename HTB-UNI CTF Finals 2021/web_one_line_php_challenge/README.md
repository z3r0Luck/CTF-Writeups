# one line php challenge (Web)

![date](https://img.shields.io/badge/date-06%2F03%2F2021-brightgreen)  
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)  
![blockchain category](https://img.shields.io/badge/difficulty-hard-red)
![hard dificulty](https://img.shields.io/badge/category-web-blue)

## Flag

``
HTB{iconv_r34lly_b3_d01ng_us_lik3_th4t}
``

## Solution

We're given a a web page where some php code is displayed. Basically if we set the bottle emoji GET parameter then eval will be executed and prints whatever the get parameter has assigned to pen emoji variable.

![challinfo](1.png "Challenge Info")

So we'll simply to execute commands with some known php functions (system, shell_exec).

![challinfo](2.png "Challenge Info")

But we get the warning that for security reasons we can't execute this function. Same applies for every other function with command execution capability, cause at the Dockerfile the configure of PHP runs with --disable-all parameter which disables all these functions.

So we can't execute command. Sad.

But next to this it has the --with-iconv parameter, and while searching for bypasses for this we came to this interesting [gist](https://gist.github.com/LoadLow/90b60bd5535d6c3927bb24d5f9955b80). It uses iconv to execute some payload on the server.