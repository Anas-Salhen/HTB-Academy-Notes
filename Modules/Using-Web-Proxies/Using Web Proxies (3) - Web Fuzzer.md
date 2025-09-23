# Burp Intruder
Burp and ZAP have features other than web proxy like fuzzers and scanners. Burp's web fuzzer is called Burp Intruder. Though it is much more advanced than most CLI-based web fuzzing tools, the free Burp Community version is throttled at a speed of 1 request per second, making it extremely slow compared to CLI-based web fuzzing tools, which can usually read up to 10k requests per second.

## Target
Select a request from proxy history, right click it then select `Send to Intruder`, or use the shortcut `CTRL+I` to send it to `Intruder`. Go to `Intruder` by clicking on its tab or with the shortcut `CTRL+SHIFT+I`. On the first tab, '`Target`', we see the details of the target we will be fuzzing, which is fed from the request we sent to `Intruder`.  
<img width="781" height="241" alt="Pasted image 20250915004544" src="https://github.com/user-attachments/assets/07cef8f2-bbb2-4589-a736-d5cf6dbb9278" />  

## Positions
The second tab, '`Positions`', is where we place the payload position pointer, which is the point where words from our wordlist will be placed and iterated over. To check whether a web directory exists we will use requests like `GET /directory/`, `GET /admin/`, etc. We will type `GET /DIRECTORY/` and then surround it with `§§` which is a placeholder that tells Burp what to replace so it becomes `GET /§DIRECTORY§/`.  
<img width="1270" height="327" alt="Pasted image 20250915143247" src="https://github.com/user-attachments/assets/1f46198d-c9d6-4590-8a4d-15baa92c9e1e" />  
The final thing to select in the target tab is the `Attack Type`. The attack type defines how many payload pointers are used and determines which payload is assigned to which position. For simplicity, we'll stick to the first type, `Sniper`, which uses only one position.

## Payloads
On this tab we choose and customize our payloads/wordlists. Each element of it would be placed and tested one by one in the Payload Position we chose earlier. There are four main things we need to configure:  
- Payload Sets
- Payload Options
- Payload Processing
- Payload Encoding

### Payload Sets
The payload set identifies the payload number, depending on the attack type and number of payloads we used in the payload position pointers. In our case the payload set is 1.

We also need to select the payload type, examples include:
- `Simple List`: The basic and most fundamental type. We provide a wordlist, and Intruder iterates over each line in it. We will use it in this example
- `Runtime file`: Similar to `Simple List`, but loads line-by-line as the scan runs to avoid excessive memory usage by Burp.
- `Character Substitution`: Lets us specify a list of characters and their replacements, and Burp Intruder tries all potential permutations.

### Payload Options
Payload options differ depending on the type we select in payload sets. For a `Simple List`, we have to create or load a wordlist. In Burp Pro, we also can select from a list of existing wordlists contained within Burp by choosing from the `Add from list` menu option.

In case you wanted to use a very large wordlist, it's best to use `Runtime file` as the Payload Type instead of `Simple List`, so that Burp Intruder won't have to load the entire wordlist in advance, which may throttle memory usage.

### Payload Processing 
Allows us to determine fuzzing rules over the loaded wordlist. For example, we can add a rule that skips the lines that start with a dot (`.`) using a regex pattern.  
<img width="838" height="237" alt="Pasted image 20250915144558" src="https://github.com/user-attachments/assets/df3c13e5-326d-4a50-946e-6c3cb63054f5" />  

### Payload Encoding
Allows us to enable or disable Payload URL-encoding. We will leave it enabled.

## Options
Example: we can set the number of `retried on failure` and `pause before retry` to 0.

`Grep - Match` enables us to flag specific requests depending on their responses, like when the response has HTTP code 200 OK. Also, disable `Exclude HTTP Headers`.

`Grep - Extract` is useful if the HTTP responses are lengthy, and we're only interested in a certain part of the response. There are also other options worth checking.

## Attack
Now that we finished the setup click on `Start Attack` and wait for it to finish.  
<img width="1053" height="235" alt="Pasted image 20250915145738" src="https://github.com/user-attachments/assets/21bf0a54-ac2d-4515-a553-da07e039d454" />  
We can see that we got one hit, `admin`. Now visit `http://<SERVER_IP>:<PORT>/admin/` and try to further test its security.

## Uses
Similarly, we can use `Burp Intruder` for other types of attacks, including brute forcing for passwords, or fuzzing for certain PHP parameters. We can even use `Intruder` to perform password spraying against applications that use Active Directory (AD) authentication such as Outlook Web Access (OWA), SSL VPN portals, Remote Desktop Services (RDS), Citrix, custom web applications that use AD authentication, and more. But, keep in mind the free version of Intruder is limited.

# ZAP Fuzzer
## Fuzz
Say we visit `http://<SERVER_IP>:<PORT>/test/`, we will locate the request in the proxy history, right-click on it and select Attack>Fuzz which will open the fuzzer window.  
<img width="1189" height="362" alt="Pasted image 20250917034847" src="https://github.com/user-attachments/assets/5ad88e30-af29-4e28-8166-2ad2279ae6d7" />  
The main things we need to configure are: 
- Fuzz Location
- Payloads
- Processors
- Options

## Fuzz Locations
Similar to payload positions in Intruder. To place a location on a word we have to select it and click `add` on the right pane and the payloads window will be opened for us.

## Payloads
We can click on the `Add` button to add our payloads and select from different types which include:
- File: This allows us to select a payload wordlist from a file.
- File Fuzzers: This allows us to select wordlists from built-in databases of wordlists.
- Numberzz: Generates sequences of numbers with custom increments.
ZAP has its own built-in wordlists and more databases can be installed from ZAP marketplace. After adding a payload we can examine it with the modify button.

## Processors
Some of the payload processors we can use:
- Base64 Decode/Encode
- MD5 Hash
- Postfix String
- Prefix String
- SHA-1/256/512 Hash
- URL Decode/Encode
- Script
In this example we selected URL encode to ensure our payloads don't cause server errors.

## Options
There are interesting options like concurrent threads per scan.

Depth first, which would attempt all words from the wordlist on a single payload position before moving to the next (e.g., try all passwords for a single user before brute-forcing the following user). 

Breadth first, which would run every word from the wordlist on all payload positions before moving to the next word (e.g., attempt every password for all users before moving to the following password).
