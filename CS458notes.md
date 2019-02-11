# CS 458

# Introduction to Security and Privacy
## Security
- Confidentiality
    - Access to systems or data is limited to authorized parties
- Integrity
    - Data is correct
- Availabilty
    - System or data is there when you want it

## Privacy
- *Informational self-determination*: You get to control information about yourself
- e.g PIPEDA is Canadian legislation for personal privacy

> Privacy vs Confidentiality
> For example, a company collecting your private information with your consent but then using it in ways that you don't understand without disclosing their methods to you is a breach of privacy because you don't have to ability to determine *how* your personal information is used. It is not a breach of confidentiality because you have consent and full knowledge that they have access to your information. On the other hand, a breach where company secrets (say a soft drink recipe) are leaked to the public is a violation of confidentiality because information is seen by unauthorized parties. It is not a breach of privacy however because (however much the legal system wants them to be) companies are not people and so there was not personally private information that was leaked.

## Other Definitions
- Assets: Things we want to protect
    - e.g Hardware, Software, Data
- Vulnerabilities: Weaknesses in a system that can be exploited
- Threats: Loss or harm that can occur to a system
    1. Interception: unauthorized access
    2. Interruption: making service unavailable
    3. Modification: tampering
    4. Fabrication: counterfeit records
- Threat model: What threats do we need to defend against
- Attack: Action that exploits a vulnerability to execute a threat
- Control/Defence: Removing or reducing a vulnerability
    1. Prevention: Prevent the attack
    2. Deterence: Make attack harder or more expensive
    3. Deflection: Make self self attractive to attack
    4. Detection: Notice attacks occuring / occured
    5. Recovery: Mitigate attack effects

## Principles
- Principle of Easiest Penetration
    - System only as strong as weakest link
- Principle of Adequate Protection
    - Security should be relative to value of the system

## Defense
- Cryptography
    - Makes data unreadable to attacker
- Software Controls
    - e.g passwords
- Hardware Controls
    - e.g fingerprint reader
- Physical Controls
    - e.g locks
- Policies and Procedures
    - e.g user privileges, password rules

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

### Implementation
- Don't use C
- Static code analysis
    - Existing software products for various languages
- Formal methods
    - Prove code does what it says it does
    - Difficult to do
- Genetic diversity
    - Use different HTTP servers, code, etc

### Change Management
- Track source code / config changes

### Code Review
- Most effective method
- Code Review Types
    - Reviewers look over the code
    - Guided walk-through: Author explains code to reviewers
- Reviewers might get lazy and miss things
    - Solution: Add easter-egg vulnerabilities

### Testing
- Black-box testing:
    - Test completed function / module / program / system without knowing the implementation
    - Fuzz-testing: give completely random data as input
        - Will often cause crashes (availability concern)
- White-box testing:
    - Testing while knowing design and implementation
    - Good for regression testing (write a test suite and re-run tests on new versions)

### Documentation
- Write down and explain design choices
- Record things that didn't work

### Maintenance
- Should have no flaws when the product is done
- There will be flaws

### Standards, process, audit
- Org should have *standards* for how each stage of software development is done
- *Processes* specify how the standards are enforced
- *Audits* verify that processes are followed

# Operating System Security

## Operating Systems
- Allows users to access to different resources in a shared way
- Identification and authorization are needed to control access
- Protects users from each other and themselves
    - Threats: attacks, mistakes, resource over comsumption, malware

## Seperation
- Physical seperation
    - User difference physical resources
    - Easy implementation
    - Expensive and inefficient
- Temporal seperation
    - Execute different programs at different times
- Logical seperation
    - User cannot see other users
- Cryptographic seperation
    - Encrypt data so others cannot read it
    - Complex

## Sharing
- Some resources should be shared (e.g library routines, files)
- Flexible sharing: ability for some files to be shared, but not all

## Memory and Address Protection
- Prevent one program from corrupting others
- Use virtual addresses instead of physical
- Techniques
    - Fence register
        - Exception if user tries to access memory below a 'fence'
        - Protects system from user
        - Single user only
    - Base/bounds register pair
        - Exception if user accesses memory below base / above bounds
        - Different values for each program
        - Limited flexibility
    - Tagged architecture
        - Additional bit in memory to indicate access rights
        - Flexible
        - Large overhead
    - Segmentation
        - Each program has multiple address spaces
        - Segments for code, data, stack
        - Virtual addresses contain a segment name and offset within segment
        - Segment table maps segment names to physical addresses
        - Segment table tracks protection attributes
        - Pro: Addresses are hardware checked for protection
            - Different levels of protection are possible
            - Users can share access
        - Con: External fragmentation
            - Costly out-of-bounds checks
    - Paging
        - Virtual memory is divided into pages
        - Physical memory is divided into frames
        - Virtual addresses contain page number and offset
        - Page table maps page number to physical address
        - Page table tracks protection attributes
        - Pro: Addresses are hardware checked for protection
            - Users can share access
            - Pages can be moved to disk
        - Con: Internal fragmentation
            - Cannot have different levels of protection

## Access Control
- Goals
    - Check every access
    - Enforce least privilege
    - Verify acceptable use

### Access Control Structures

#### Access Control Matrix
- Objects (O): Files / database records
- Subjects (S): users, processes
- Rights (R): read, write, execute, own
- Table permissions for each user and file
- Matrix very sparse

#### Access Control List
- Implementation of access control matrix
- More space efficient
- Each object has a list of subjects and their rights
- Use cases
    - Determine set of allowed users per object: fast
    - Determine set of objects a user can access: slow
    - Revoke a user’s access right to an object or all objects: 1 object: medium, all objects: slow

#### Capabilities
- A capability is an **unforgeable token** that gives its owner some access rights to an object
- More versatile than access control lists
- Can be transferable (if anonymous)
- e.g seL4 is an OS that uses capabilities
- Use cases
    - Determine set of allowed users per object: slow
    - Determine set of objects a user can access: fast
    - Revoke a user’s access right to an object or all objects: 1 object: fast, all objects: slow

#### Combined ACL and Capabilities
- Faster access checking
- e.g UNIX: every file has an ACL, if access is granted, caller is given a capability stored in kernel
- Problem: TOCTTOU vulnerability

#### Role-based access control
- Access depends on group that user belongs in
- Advantage: Role changes are easy to change permissions for
- Extensions: Hierarchical roles
    - Multiple roles
    - Separation of duty

## User Authentication
- Computer systems must *identify* and *authenticate* users before *authorizing* them
- *Identification*: Who are you
- *Authentication*: Prove identity

### Authentication Factors
- 4 classes of authentication factors using something that the user...
    1. Knows (e.g Password, PIN, secret question)
    2. Has (ATM card, badge, browser cookie, physical key)
    3. Is (Biometrics)
    4. Context (Location, time, devices in proximity)
- Combine factors for multifactor / 2-factor authentication

#### Passwords
- Oldest authentication mechanism
- Usability problems:
    - Inconvenient
    - Password composition rules
    - Updating shared passwords
- Security problems
    - Leaked passwords are easy to use
    - Shoulder surfing
    - Keystroke logging
    - Interface illusions
    - Password re-use
    - Password guessing
    - Interception attacks

##### Password guessing
- Brute-force
    - $2700 can break a 8 character password in 6 minutes
    - Exponential difficulty depending on number of characters
- Dictionary attack
    - Passwords are not uniformly random
    - Prefix or postfix numbers
- Offline attack
    - Requires access to encrypted password file
- Online attack
    - Detectable by bank server (n login attempts)
- Defense: use a MAC (hash with a key)
    - Key must be stored elsewhere

##### Password Hygiene
- Use a password manager
    - At least for low- and medium-security passwords
    - Helps agaisnt phishing attacks
- Or Use a pass phrase of randomly chosen words
    - More random and secure
- Have site-specific passwords
- Don't reveal passwords to others
- Don't enter passwords on public computers

##### Password Policies
- No composition rules
- 8 <= len <= >64
- Allows any characters including space, unicode, emoji
- Blacklist common passwords, compromised passwords
- Avoid password hints
- Dont require password cycling
- Allow copy-pasting passwords in fields
- Use two-factore authentication
    - Don't use SMS: it can be intercepted

##### Storing Passwords
- Store a *digital fingerprint* of the password, not plain-text
- Use a crytographic hash to create the fingerprint
    - Don't use standard ones (SHA-1, SHA-512) that are cheap to compute
    - Use an iterated hash function that are expensive to compute (bcrypt) and lots of memory (scrypt)
    - ^Same requirements of cryptocurrencies
- Include a user-specific salt in the fingerprint
    - This changes the fingerprints even if the password is the same

##### Password Recovery
- Not recommended
- Cannot be recovered from the fingerprint (otherwise the attacker can too)
- Encrypt the password so that it can be decrypted
- e.g Adobe Password Hack when Adobe did not use a secure password encryption

#### Alternative Passwords
- Android unlock patterns
    - Easy to dictionary attack
    - Can't be automated
- Graphical passwords
    - e.g Re-Identify a picture
    - e.g Choose a set of places in a picture
    - Problem: Shoulder surfing
- Biometrics
    - Can accept someone else that is sufficiently close
    - Doesn't work well for remote authentication
    - Identification is harder (expensive search problem)
        - Base rate fallacy for identifying criminals
    - Privacy issues
    - Accuracy issues: False negatives, appearance changes
    - Secrecy: Face, fingerprints aren't very secret
    - Legal Protection: Police can make you use your finger, but not reveal your password

> End Midterm material
> No security policies and models (it's outdated XD)
> Format: Written, no calculators
> Study: Look at notes
>> Definitions
>> No fine details (names of malware)
>> High level understanding of assignment

## Security Policies and Models

### Trusted Operating Systems
- We have *confidence* that it provides security services
    - Memory and file protection
    - Access control and user authentication
- 4 factors
    - Policy: rules outlining what's secured and why
    - Model: implements the policy
    - Design: specs of how the model is implemented
    - Trust: assurance implementation is to design

### Trusted Software
- Trust that code does what is expected and nothing more
- Factors
    - Functional correctness: works correctly
    - Enforcement of integrity: wrong inputs don't impact data correctness
    - Limited privilege: access rights are minimized
    - Appropriate confidence level: rated as required by environment

### Security Policies

#### Military
- Everything has a sensitivity
    - "Top Secret" > "Secret" > "Confidential" > "Unclassified"
- Everything has 1+ compartment
    - i.e Groups
- Subject s can access object o if and only if
    - level(s) >= level(o)
    - compartments(s) $\supseteq$ comparments(o)

#### Commercial
- Classification levels different
    - e.g external vs internal
- Clearance levels less formally defined

#### Other
- Non confidentiality policies
- Integrity policy: Clark-Wilson Security Policy
    - Use *well-formed transactions* to ensure state is always consistent
- Conflicts of interest: Chinese Wall Security Policy
    - Once you are on a side, it is hard to switch
    - Once you access information about a company, you cannot access information of similar companies
    - Requirements
        - History of object access
        - Changing access rights
    - use cases: Consulting, legal, accounting
    - *ss-property*: Subject `s` can access object `o` if and only if
        - each previously accessed object belongs to the same company of `o` or belongs to a different kind of company
    - *\*-property*: For `s` to write to `o`,
        - all objects readable by `s` belong to the same company as `o` or have been sanitized
        - This prevents indirect information flow

### Security Models
- 2 multilevel security (MLS) policies

#### Side: Lattice
- Multilevel security (MLS) policies contain clearance and classification levels

#### Bell-La Padula Confidentiality Model
- Regulates information flow in lattice
- Information only flows *up*
- *ss-property*: "No read up"
    - `s` can only read `o` if
- *\*-property*: "No write down"

#### Biba Integrity Model
- Prevents inappropriate data modification
- Write:
    - `s` can modify `o` if ....
- Read: TODO
- Very restrictive access rules
- Information flows down

##### Low Watermark Property

## TODO: Rest of Security policies and models

## Trusted operating system design
- Reading

### Security design principles
- Least privilege
    - Operate using fewest privileges possible
- Economy of mechanism
    - Protection mechanism should be as simple as possible
- Open design
    - Avoid security by obscurity
    - Don't use secret algorithms
- Complete mediation
    - Every access attempt must be checked (TOCTTOU)
- Permission based / fail-safe defaults
    - Default to no access
- Seperation of privileges
    - Check multiple conditions to get access
- Least common mechanism
- Ease of use


## TODO

### Trusted computing base (TCB)

### Virtualization
- Virtual memory
    - Illusion that every device is the only device that has access to the machine
- Virtual machine
    - Sandbox for applications
    - Higher security
    - Attackers can launch your OS in a VM
        - Hard to detect, and attackers can see everything

### Application Insulation
- Use encryption to sheild memory of one application from another
- Seperate application into trusted and untrusted code
- API to communicate between untrusted and trusted code
- e.g Intel SGX (Software guard extension)
    - CPU stores a key for encryption
    - Originally for DRM -> realized DRM is not worth it
- e.g AMD memory encryption

### Signal Contact Discovery
- Signal = contacts?
- How do you know something has signal, so that you can use it to communicate?
    - Users send hash of their contacts phone numbers to signal server
    - Server matches up hashes to determine if two users can use Signal send encrypted numbers
- Hashes are not salted so attackers can easily construct social graphs
- Solution: Seperate out trusted and untrusted code
    - Secure Enclave (i.e SGX) receives encrypted contacts information and decrypts it
    - Rest of server does not have access to decrypted information
    - ^ **Remote attestation**

### Least Privilege in popular OS
- OS privilege control was really bad
- Windows:
    - pre-NT: any user can do anything
    - pre-Vista: needs admin account to do some things (but everyone just ran as admin)
    - Vista: temporary access rights, integrity levels
- UNIX:
    - root has every access
    - root was removed in some later UNIX OS
- iOS:
    - Original iphone had user run everything as root (only 1st party apps, so more ok)
    - Every app had a user id with different access

### Seperation Techniques

#### Chroot
- `chroot /new/root command`
- Create a 'jail' which is the only space the command is allow to touch
- The root directory for the command is changed to something else
- Can be broken out of

#### Compartmentalization
- Split application into parts that have least privilege
- Have an unprivileged, jailed child that takes unsafe unput

#### `setuid` / `suid` bit
- Makes the executable run under identity of the owner, not caller
    - e.g `pwgen` runs under `root`, not `user`
- Vulnerable to privilege escalation attack
- Confused deputy attack: impersonate another user

### Assurance
- How to convince people an OS is secure?
- Testing
    - Cannot prove no problems exist
    - (halting problem...?)
- Formal verification
    - Use math to prove OS correctness
    - Not advanced enough for large OSs
- Validation
    - Requirements checking, design, code reviews, system testing
- Evaluation
    - Have a trusted entity evaluate and certify the OS
    - *Orange Book* gives a D to A1 rating
    - *Common Criteria* replaces the Orange book
        - Has Protection Profiles
        - Rating from EAL1 (worst) to EAL7 (best)
    - Most OSs have C rating

# Network Security
- Not built with security in mind

## Network Concepts

### Architecture
- Information is routed between endpoints
    - e.g router, server, mobile, doorbell, thermostat
- local ISP -> regional ISP -> backbone -> regional ISP -> company network

### Characteristics
- No single entity controls the internet
    - fuzzy... Amazon going down would take down a lot of websites
- Traffic flows through nodes controlled by different entities
- Cannot control path of traffic flow
- TCP/IP is a network protocol

#### TCP/IP Protocol Suite
- 4 layers
    - Link
        - How things are physically connected (wifi, ethernet)
    - Network
        - IP addresses
    - Transport
        - TCP or UDP
        - Control information
    - Application
        - HTTP, FTP, email, SSL
- Was designed for local network connections between trusted entities
- Design did not address malicious errors

## Threats in Networks
- "No one knows if you're a dog on the internet"
- Things on the internet are very visible
- DOS: Attack on availability

### Confidentiality Attacks

#### Port Scan
- Attacker sees which ports are open on a server
    - Indicates which applications are running on the server
- Server responses can reveal software versions being used
- Loose lipped Login pages can reveal which user_id's are valid
    - Timing Attack: Response times differ if the user exists because password hashing takes time
    - e.g Ashley Madison has a different password recovery screen if the email exists

#### Intelligence
- Social Engineering
    - e.g Twitter handle attacks
    - Attacker impersonates the user and attempts to reset password
- Dumpster Diving
    - Collecting as much information as possible
- Eavesdropping
- Google
    - Lots of information on the internet that can be found
- Social Networks
    - Lots of personal information on social networks

#### Eavesdropping
- Owner of node can monitor communication flowing through node
    - End-to-end encryption makes this better
- Active wiretapping: modification or fabrication of communication
- Communication can be accidentally sent to an attacker's node
- Assume your communication is wiretapped

##### Communication Media
- Copper cable
    - Inductance allows nearby attackers to eavesdrop
    - Can also splice in a secondary cable
- Optical fibre
    - No inductance, and splicing will cause detectable signal loss
- Microwave / Satellite communication
    - Attackers close to receiver can eavesdrop
- These attacks involve physical expenses and effort
- WiFi
    - Easily intercepted by anyone with a WiFi-capable device with the right software
    - Can be tapped by people kilometers away

#### Misdelivered information
- Local Area Network (LAN)
    - Connects all computers in a company
    - Packets may be sent to multiple nodes
    - Simple configuration to keep the packets instead of deleting them
- Email
    - Wrongly addressed emails
    - Accidental reply-all

#### Impersonation
- Impersonate people by stealing their password
    - Guessing password
    - Using default passwords
    - Password sniffing (e.g unsecured networks)
    - Social engineering
- Exploit trust between machines or accounts
    - `rhosts` allows (user A on machine X) to specify (user B on machine Y) can act as (A on X) without a password
    - `ssh` has a similar mechanic in `.ssh/authorized_keys`

#### Spoofing
- An Object masquerades as another one
- URL spoofing (phishing websites)
    - Exploit typos, ambiguities, similarities
    - Defence: buy out domains that look similar
- "Evil Twin" attack
    - Create WiFi access points that are named the same
    - You can get people's credentials when they log into your network
- Cellphone tower spoofing (MSI catchers)
    - Gets identities of people
- Session hijacking
    - TCP protocol has state that keeps track of lost packets
    - Attacker can perform TCP hijacking and masquerade as one of the endpoints
        - Also used to bypass censorship firewalls
    - Cookies can be sniffed or stolen and used to masquerade as the client
- Man-in-the-middle attacks
    - Attacker becomes a stealth intermediate node, and passes traffic on to the end node

#### Traffic Analysis
- The existence of communication is sensitive
    - e.g Whistle blowers
    - e.g Large pizza order = meeting
- TCP/IP packets include sender and receiver node information
    - Easy to analyze

### Integrity Attacks
- Attackers can modify packs while they are being transmitted
    - Change payloads
    - Change sender or receiver
    - Replay previous packets
    - Delete or create packets
- There are checks to detect natural packet drops and corruption
    - e.g TCP checksum
    - Attackers can get around the checks
- DNS cache poisoning
    - Domain Name System maps host names to IP addresses
    - Attacker can create wrong mappings
        - Run own DNS server
        - Modify traffic
    - Defence: DNSSEC
        - Still being rolled out

#### Protocol Failures
- TCP/IP assumes all nodes implement protocols faithfully
- Congestion control that asks a sender node to slow down can be ignored
- Check that packets are well formatted
    - Vulnerable to out of bounds errors from bad lengths
- Some protocols are complex and hard to implement correctly
    - e.g TLS
- Some protocols are just broken
    - e.g WEP

### Website Vulnerabilities
- Website defacement
- HTML response can reveal server version information (loose-lips)
- Malicious URLs can be sent to a server 
    - Buffer overflow attacks
    - Sensitive file access
    - Feed malicious input to a server-side script
- HTTP doesn't have state, and relies on client to keep state
    - Attackers can modify the state
- XSS, CSRF
- Embedding HTML code into someone else's webpage

#### Denial of Service (DoS)








