# Functions
Functions must always be defined logically `before` the first call since a script is also processed from top to bottom. They can be defined in 2 ways:
```bash
function name {
	<commands>
}
```
Or
```bash
name() {
	<commands>
}
```
In our script we defined:
```bash
<SNIP>
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
<SNIP>
```
Then called it simply by typing its name:
```bash
<SNIP>
case $opt in
	"1") network_range ;;
	"2") ping_host ;;
	"3") network_range && ping_host ;;
	"*") exit 0 ;;
esac
<SNIP>
```

## Passing Parameters
```bash
#!/bin/bash

function print_pars {
	echo $1 $2 $3
}

one="First parameter"
two="Second parameter"
three="Third parameter"

print_pars "$one" "$two" "$three"
```
When we run the script above it will print: `First parameter Second parameter Third parameter`.

## Return Values
When we start a new process, each `child process` (for example, a `function` in the executed script) returns a `return code` to the `parent process` (`bash shell` through which we executed the script) at its termination, informing it of the status of the execution.

|**Return Code**|**Description**|
|---|---|
|`1`|General errors|
|`2`|Misuse of shell builtins|
|`126`|Command invoked cannot execute|
|`127`|Command not found|
|`128`|Invalid argument to exit|
|`128+n`|Fatal error signal "`n`"|
|`130`|Script terminated by Control-C|
|`255\*`|Exit status out of range|
In the following example we get the return value of the function and even pass its result to a variable:
### Return.sh

```bash
#!/bin/bash

function given_args {

        if [ $# -lt 1 ]
        then
                echo -e "Number of arguments: $#"
                return 1
        else
                echo -e "Number of arguments: $#"
                return 0
        fi
}

# No arguments given
given_args
echo -e "Function status code: $?\n"

# One argument given
given_args "argument"
echo -e "Function status code: $?\n"

# Pass the results of the funtion into a variable
content=$(given_args "argument")

echo -e "Content of the variable: \n\t$content"
```

```shell-session
[!bash!]$ ./Return.sh

Number of arguments: 0
Function status code: 1

Number of arguments: 1
Function status code: 0

Content of the variable:
    Number of arguments: 1
```

# Debugging
Can be done using `-x` and `-v` like: `bash -x CIDR.sh` or `bash -x -v CIDR.sh`.
