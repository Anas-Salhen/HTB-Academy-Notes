Last Update: 2025-08-24 5:51 AM
# Introduction
[Bash](https://en.wikipedia.org/wiki/Bash_\(Unix_shell\)) or Bourne Again Shell is the scripting language used in Unix-based systems. We can also use it in windows via [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about). The main difference between scripting and programming languages is that we don't need to compile the code to execute the scripting language, unlike programming languages. To execute a script, we have to specify the interpreter and the script it will execute.  Examples of running Bash scripts:
```shell-session
AnasSalhen@htb[/htb]$ bash script.sh <optional arguments>
```

```shell-session
AnasSalhen@htb[/htb]$ ./script.sh <optional arguments>
```

In the following example we will run a script we made with some input:
```shell-session
AnasSalhen@htb[/htb]$ ./CIDR.sh inlanefreight.com

Discovered IP address(es):
165.22.119.202

Additional options available:
	1) Identify the corresponding network range of target domain.
	2) Ping discovered hosts.
	3) All checks.
	*) Exit.

Select your option: 3

NetRange for 165.22.119.202:
NetRange:       165.22.0.0 - 165.22.255.255
CIDR:           165.22.0.0/16

Pinging host(s):
165.22.119.202 is up.

1 out of 1 hosts are up.
```
The code for the script:
```bash
#!/bin/bash

# Check for given arguments
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
else
	domain=$1
fi

# Identify Network range for the specified IP address(es)
function network_range {
	for ip in $ipaddr
	do
		netrange=$(whois $ip | grep "NetRange\|CIDR" | tee -a CIDR.txt)
		cidr=$(whois $ip | grep "CIDR" | awk '{print $2}')
		cidr_ips=$(prips $cidr)
		echo -e "\nNetRange for $ip:"
		echo -e "$netrange"
	done
}

# Ping discovered IP address(es)
function ping_host {
	hosts_up=0
	hosts_total=0
	
	echo -e "\nPinging host(s):"
	for host in $cidr_ips
	do
		stat=1
		while [ $stat -eq 1 ]
		do
			ping -c 2 $host > /dev/null 2>&1
			if [ $? -eq 0 ]
			then
				echo "$host is up."
				((stat--))
				((hosts_up++))
				((hosts_total++))
			else
				echo "$host is down."
				((stat--))
				((hosts_total++))
			fi
		done
	done
	
	echo -e "\n$hosts_up out of $hosts_total hosts are up."
}

# Identify IP address of the specified domain
hosts=$(host $domain | grep "has address" | cut -d" " -f4 | tee discovered_hosts.txt)

echo -e "Discovered IP address:\n$hosts\n"
ipaddr=$(host $domain | grep "has address" | cut -d" " -f4 | tr "\n" " ")

# Available options
echo -e "Additional options available:"
echo -e "\t1) Identify the corresponding network range of target domain."
echo -e "\t2) Ping discovered hosts."
echo -e "\t3) All checks."
echo -e "\t*) Exit.\n"

read -p "Select your option: " opt

case $opt in
	"1") network_range ;;
	"2") ping_host ;;
	"3") network_range && ping_host ;;
	"*") exit 0 ;;
esac
```
The comments split the script into these parts:
1. Check for given arguments
2. Identify network range for the specified IP address(es)
3. Ping discovered IP address(es)
4. Identify IP address(es) of the specified domain
5. Available options

# Conditional Execution
## Shebang
The shebang line is always at the top of each script and always starts with "`#!`". This line contains the path to the specified interpreter (`/bin/bash`) with which the script is executed. We can also use Shebang to define other interpreters like Python, Perl, and others.
```python
#!/usr/bin/env python
```

```perl
#!/usr/bin/env perl
```

## If-Else-Fi
```bash
# Check for given argument
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
else
	domain=$1
fi
```
This code from the script earlier translates to:
```bash
if [ the number of given arguments equals 0 ]
then
	Print: "You need to specify the target domain."
	Print: "<empty line>"
	Print: "Usage:"
	Print: "   <name of the script> <domain>"
	Exit the script with an error
else
	The "domain" variable serves as the alias for the given argument 
finish the if-condition (fi)
```
We can also add `elif` to match more cases like:
```bash
value=$1

if [ $value -gt "10" ]
then
	echo "Given argument is greater than 10."
elif [ $value -lt "10" ]
then
	echo "Given argument is less than 10."
else
	echo "Given argument is not a number."
fi
```
Knowing this, we can use it to improve the original script:
```bash
# Check for given argument
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
elif [ $# -eq 1 ]
then
	domain=$1
else
	echo -e "Too many arguments given."
	exit 1
fi
```

# Arguments, Variables, and Arrays
## Arguments
We can pass up to 9 arguments (`$0`-`$9`) to the script without assigning them to variables or setting the corresponding requirements for these. `9 arguments` because the first argument `$0` is reserved for the script. We need the dollar sign (`$`) before the name of the variable to use it at the specified position.
```shell-session
AnasSalhen@htb[/htb]$ ./script.sh ARG1 ARG2 ARG3 ... ARG9
       ASSIGNMENTS:       $0      $1   $2   $3 ...   $9
```

## Special Variables
Special variables use the [Internal Field Separator](https://bash.cyberciti.biz/guide/$IFS) (`IFS`) to identify when an argument ends and the next begins. Some special variables:

|**Special Variable**|**Description**|
|---|---|
|`$#`|This variable holds the number of arguments passed to the script.|
|`$@`|This variable can be used to retrieve the list of command-line arguments.|
|`$n`|Each command-line argument can be selectively retrieved using its position. For example, the first argument is found at `$1`.|
|`$$`|The process ID of the currently executing process.|
|`$?`|The exit status of the script. This variable is useful to determine a command's success. The value 0 represents successful execution, while 1 is a result of a failure.|

## Variables
```bash
<SNIP>
else
	domain=$1
fi
<SNIP>
```
here we see that we assign the value of the first argument to the variable called "`domain`". The assignment of variables takes place without the dollar sign (`$`). The dollar sign is only intended to allow this variable's corresponding value to be used in other code sections. When assigning variables, there must be no spaces between the names and values (`domain = $1` is wrong). 

In contrast to other programming languages, there is no direct differentiation and recognition between the types of variables in Bash like "`strings`," "`integers`," and "`boolean`." All contents of the variables are treated as string characters. Bash enables arithmetic functions depending on whether only numbers are assigned or not. All defined variables are always processed `globally` unless otherwise declared by "[local](https://www.tldp.org/LDP/abs/html/localvar.html)."

## Arrays
Example:
```bash
#!/bin/bash

domains=(www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com www2.inlanefreight.com)

echo ${domains[0]}
```
Running this script outputs: `www.inlanefreight.com`  

When using (' ') or (" ") all spaces between the single and double quotes are ignored and handled as a single value assigned to the array.
```bash
#!/bin/bash

domains=("www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com" www2.inlanefreight.com)
echo ${domains[0]}
```
Running this script outputs: `www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com`

# Comparison Operators
## String Operators

| **Operator** | **Description**                             |
| ------------ | ------------------------------------------- |
| `==`         | is equal to                                 |
| `!=`         | is not equal to                             |
| `<`          | is less than in ASCII alphabetical order    |
| `>`          | is greater than in ASCII alphabetical order |
| `-z`         | if the string is empty (null)               |
| `-n`         | if the string is not null                   |
These operators are used to compare strings. It is important to note here that we put the variable for the given argument (`$1`) in double-quotes (`"$1"`). This tells Bash that the content of the variable should be handled as a string. Otherwise, we would get an error. Example:
```bash
<SNIP>
# Check the given argument
if [ "$1" != "HackTheBox" ]
then
	echo -e "You need to give 'HackTheBox' as argument."
	exit 1
fi
<SNIP>
```

String comparison operators "`<` / `>`" works only within the double square brackets `[[ <condition> ]]`. We can find the ASCII table on the Internet or by using `man ascii` in the terminal.

## Integer Operators
| **Operator** | **Description**             |
| ------------ | --------------------------- |
| `-eq`        | is equal to                 |
| `-ne`        | is not equal to             |
| `-lt`        | is less than                |
| `-le`        | is less than or equal to    |
| `-gt`        | is greater than             |
| `-ge`        | is greater than or equal to |

## File Operators
| **Operator** | **Description**                                        |
| ------------ | ------------------------------------------------------ |
| `-e`         | if the file exist                                      |
| `-f`         | tests if it is a file                                  |
| `-d`         | tests if it is a directory                             |
| `-L`         | tests if it is if a symbolic link                      |
| `-N`         | checks if the file was modified after it was last read |
| `-O`         | if the current user owns the file                      |
| `-G`         | if the file’s group id matches the current user’s      |
| `-s`         | tests if the file has a size greater than 0            |
| `-r`         | tests if the file has read permission                  |
| `-w`         | tests if the file has write permission                 |
| `-x`         | tests if the file has execute permission               |
Example:
```bash
<SNIP>
# Check if the specified file exists
if [ -e "$1" ]
then
	echo -e "The file exists."
	exit 0
<SNIP>
```

## Logical Operators
| **Operator**      | **Description**        |
| ----------------- | ---------------------- |
| `!`               | logical negotation NOT |
| `&&`              | logical AND            |
| <code>\|\|</code> | logical OR             |
These can be used to chain conditions together.

# Arithmetic
| **Operator** | **Description**                         |
| ------------ | --------------------------------------- |
| `+`          | Addition                                |
| `-`          | Subtraction                             |
| `*`          | Multiplication                          |
| `/`          | Division                                |
| `%`          | Modulus                                 |
| `variable++` | Increase the value of the variable by 1 |
| `variable--` | Decrease the value of the variable by 1 |
`${#variable}` gives us the number of characters in the variable.
