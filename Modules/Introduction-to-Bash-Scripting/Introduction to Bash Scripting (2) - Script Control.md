# Input and Output
## Input Control
```bash
read -p "Select your option: " opt
```
Reads input from the user and stores it in the opt variable.

## Output Control
Redirection (like `>` and `>>`) is useful in writing output to a file but the problem is no output is shown on our screen since it's being sent to a file. However, when we use `tee` like: `netrange=$(whois $ip | grep "NetRange\|CIDR" | tee -a CIDR.txt)` output is sent to the file and shown in the terminal at the same time. The `-a` parameter ensures that we append to the file not overwrite it.

# Flow Control - Loops
## For Loops
Examples:
```bash
for variable in 1 2 3 4
do
	echo $variable
done
```

```bash
for ip in "10.10.10.170 10.10.10.174 10.10.10.175"
do
	ping -c 1 $ip
done
```
We can also write it in our terminal:
```shell-session
AnasSalhen@htb[/htb]$ for ip in 10.10.10.170 10.10.10.174;do ping -c 1 $ip;done

PING 10.10.10.170 (10.10.10.170): 56 data bytes
<SNIP>
```

## While Loops
```bash
<SNIP>
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
<SNIP>
```
To avoid while running forever we can either use a counter (like above) so the condition to run `while` becomes false at some point or use `break`. Example:
```bash
<SNIP>
counter=0

while [ $counter -lt 10 ]
do
  # Increase $counter by 1
  ((counter++))
  echo "Counter: $counter"

  if [ $counter == 2 ]
  then
    continue
  elif [ $counter == 4 ]
  then
    break
  fi
done
<SNIP>
```

## Until Loops
Just like `while` with the difference that the code in `until` works as long as the condition is false (unlike `while` which works as long as the condition is true).

# Flow Control - Branches
Branches in flow control include `if-else` which we already studied and `case` (also known as `switch-case`) statements. In `case` statements we have a variable and multiple possible values for it. Each possible value has some code that will be executed if it's the value of the variable. Syntax:
```bash
case <expression> in
	pattern_1 ) statements ;;
	pattern_2 ) statements ;;
	pattern_3 ) statements ;;
esac
```
Example from the script earlier:
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
