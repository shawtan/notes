# CS 458

# Introduction to Security and Privacy
- TODO

# Program Security

## Format String Vulnerabilities
- Seg fault means you are in the right place
- Print '%s'x1000 until you get a seg fault
- Execute with '%n'

## Malware
### Viruses
- Requires user interaction to spread

#### Payload
- Any part of the virus code that is not the self replicating part
- Extra code

#### Spotting Viruses
- Look at new files, occasionally do a full computer scan
- Signature-based protection
    - Maintain list of all known viruses and its characteristic features
    - Payload code, infection code are usually unique
    - Can also identify system locations and propagation means
    - Cons: Fooled by polymorphic viruses
    - Cons: Can't identify new viruses
    - No false positives
    - High false negatives
- Behaviour-based protection
    - Look for suspicious patterns of behaviour
    - Run new code in a sandbox first
        - Some viruses can detect if they are in a sandbox
    - Medium false positives
    - Medium false negatives
- False negatives are worse than false positives
- Base Rate Fallacy (Wacky probabilities when the base rate is low)

#### Polymorphic viruses
- Viruses can alter themselves to escape signature based detection
- e.g Decryption routine followed by encryted code
- Include jumps to mix up instruction order
- Add NOPs
- Set `x=0` as `x=x-x` or `x=x*0`

### Worms
- Spreads with no or little user involvement
    1. Infects your computer
    2. Searches for other computers (over network) to infect
    3. May have a payload that activates by a trigger
- e.g Morris Worm: First usage of Buffer Overflow exploit
- e.g Stuxnet: Targets Iranian nuclear centrifuges

### Trojans
- Package useful code with malicious code so that users will run the code
- >Scareware
    - Windows IE phishing website warning
- >Ransomware
    - Cryptolocker demanded bitcoin to decrypt user's files

### Logic Bombs
- Malicious code already on your computer
- Executes after a trigger
- Usually written by an insider to be triggered later
- e.g Easter Eggs are non-malicious logic bombs

## Other Malicious Code
### Web Bugs
- Object embedded in a web page that is from a different server than the webpage
- Used for advertising tracking
- Privacy violation

### Back Doors
- Set of instructions designed to bypass the normal authentication mechanism
- Examples
    - Debugging back door left in
    - Planted by Code Red worm
    - Port knocking
- Sources
    - Forgot to remove
    - Left for testing / maintenance / legal
    - Left for malicious reasons

### Salami Attacks
- Made of many small, inconsequential attacks
- e.g Multiple transactions exploiting Round off error
- e.g Multiple small charges to many cards

### Privilege escalation
- Goal of many attacks
- Raises the privilege level of the attacker
- e.g rlogin host -l userid="value"
    - use "-froot" as the userid
- e.g Packet sniffing of HTTP packets

### Rootkit
- Packed tools for privilege escalation
- Has stealth capabilities to hide its own existence <- key part
    - Cleans up log messages
    - Modifies `ls` and `ps` commands to not report files and processes belonging to the rootkit
    - Or even modifies the kernel to hide its existence
- e.g Sony XCP
    - Sony audio CDs contain an `autorun.exe` that modifies the cd driver to not read XCP-protected CDs
    - Made self difficult to find and uninstall

### Keystroke Logging
- Almost all information input comes from keyboard
- Can be accessed locally or sent to a remote machine
- Installed by malware or family members
- Types
    - Application-specific (to reduce noise and amount of data)
    - System
    - Hardware (between keyboard and computer. completely undetectable)

### Interface Illusions
- e.g scareware
- Mimicks standard interfaces but actually does other behaviour
- e.g Dragging scrollbar actually drags a program into the startup folder
- We expect interfaces to look a certain way and trust them
- Defense: hard to defend against. Focus on mitigation instead
    - OS requires 'ctrl-alt-del' to login
    - Banks have security images

#### Phishing
- Form of interface illusion
- Malicious website that looks like a reputable one
- Login information is given to the attacker
- Detection:
    - Unusual email (requires urgent action)
    - Unusual URL
    - Attachments with uncommon names
    - Typos, unusual wording
    - HTTPS does not guarentee
- Spearphishing = phishing attack targetting specific person

### Man-in-the-middle attacks
- Category for keyboard logging, inferface illusions and phishing
- The website/program/system you're communicating with is fake
- User doesn't notice problems because the program passes things on the actual site
- Can edit output to hide it's attack

## Non-malicious Flaws

### Covert Channels
- Transfering data through a channel not meant to transmit that information

### Side Channels
- Watch how computer behaves when processing sensitive data
- Usually need to be in physical vicinity
- e.g
    - Bandwidth consumptions
    - CPU fan noise / temperature
    - Shoulder-surfing
    - Reflections: Look at reflection in the mirror or glasses
        - Telescope + Camera < $2000

## Controls against security flaws in program
- Add security controls to all stages of the software lifecycle

### Design
- Modularity
    - Break problems into small pieces responsible for a single subtask
    - Easier to check for flaws
    - Have low coupling between modules
- Encapsulation
    - Have modules be self-contained
    - Reduces coupling
- Information Hiding
    - Internals of one module shouldn't be visible to the other modules
- Mutual Suspicion
    - Check inputs before processing
    - Prevents corrupted data in one module from corrupting other modules
- Confinement
    - Sandbox potentially untrustworthy code

