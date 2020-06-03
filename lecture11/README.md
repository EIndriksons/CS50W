# Lecture 11 - Security


## Open Source Software
Open-source software’s code is openly available to anyone who would like to see it or develop and contribute to it. In terms of security, open-source software could be considered more secure because it can be seen by anyone. On the other hand, any security vulnerabilites can be exploited by anyone who can find them.

**Therefore:**
- Make sure which codebase you show to the public / keep open source, and which you don't.


## Services (Git, GitHub, Travis, etc.)
With GitHub in particular, repositorires can be made private and restricted to certain users, but if someone gains access to a GitHub account, those repositories are still easily accessed.

Sites could try to prevent simple account hacking by limiting log-in attempts to a given frequency, by implementing a more rigorous user system that employs two-factor authentication, for example. **Two-factor authentication** requires access to a phyiscal device, such as a phone, to verify user identity.

With Git or other version control systems in general, it is important that **sensitive information leaks**, like a password or information, doesn’t get pushed to a repository. If such a situation were to occur, even pushing another commit removing those credentials wouldn’t be secure. Due to the nature of version control, all old commits are still visible.

It is possible to revert to an old commit and prune away extra commits and force push this back to GitHub, but all that code should be considered compromised. Any compromised credentials should be exchanged with new ones.

**Therefore:**
- Make sure to properly secure any services that have access to your source code (GitHub, Travis, AWS, Heroku, etc.) or sensitive information
- Make sure to not push into Git sensitive information like passwords or private keys


## HTML
Because any page’s HTML source code can be easily viewed and copied, a bank’s website’s HTML could be replicated to deceive users into inputting their credentials.

Links in particular are easily abused. Any links can be modified to redirect users to a different web page entirely to trick a user into doing something. The inner text of a link, which is displayed to a user, can be changed to be completely different from what the actual link is.

From the user’s end, one way to defend against these security vulnerabilites is to be careful about what links are being clicked. Web browsers often display in a status bar or some other UI the actual link.

Ultimately, there’s no way to avoid a site’s HTML from being viewed or copied because the server has to send to HTML source code to a user in order for the web page to be rendered by the browser.

**Therefore:**
- Make sure to inform your users about any phising attacks, possibility that someone might copy your front-end on a different, similar sounding domain name and redirect your users to a fake website, etc.


## Servers (like Flask, Django, etc.)
With any web server, packets of information are being sent between routers, which opens up new security concerns. Often times, a single request sent to a server will travel through multiple routers. Any of those midway points could potentially read any of the information being passed.

![Servers](img/0-servers.png)

### Cryptography
Cryptography is the process of encrypting traffic flowing through these routers so that a middleman cannot read the data.

**Secret-key cryptography** consists of both the sender and the recipient both know a secret key which can be used, along with a cryptographic algorithm, to encode and decode the message. The encrypted version is called **ciphertext**. The unencrypted version is **plaintext**.

In order for this system to work, only the sender and the recipient can know the key, which means that the key cannot be transmitted along with ciphertext.

![Secret-key cryptography](img/1-secret-key-crypto.png)

**Public-key cryptography** uses two keys, a **public key** and a **private key**. The public key can only be used to encrypt information, while the private key, which should never be shared or sent across a network, can be used to decrypt information.

Every time a message needs to be sent, the recipient sends their public key, which can be known, to the sender, who uses the key to encrypt the message. The recipient’s private key is the only key which can be used to decrypt the ciphertext. It doesn’t matter, then, that intermediaries might have the public key.

![Public-key cryptography](img/2-public-key-crypto.png)

### Environmental variables
As has been noted, passwords and other credentials should never be put in source code. What should be done instead is to set parameters like secret keys using environment variables, which are located inside the system the program is running on but not in the program’s code itself.

```
app.config["SECRET_KEY"] = "dHdlbnR5ZWlnaHQ                 # Bad practice
app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")     # Good practice
```

**Therefore:**
- Make sure your app is using secure http/https protocols
- Make sure you are storing your sensitive keys, passwords, database credentials, etc. in environmental variables


## SQL

### Hashing Passwords
When storing user information, such as username and passwords, in a database, sensitive information like passwords should be encrypted. Specifically, the **hashed** version, which is the output of a hash function which deterministically generates a sequence based on the input, which is the plaintext password.

Hashed functions are generally designed to be one-way, such that it is unfeasible to decrypt a hashed password. To log a user in, the entered password simply needs be run through the (deterministic) hash function and compared with the stored hash. If a hash function is known, malicious users who have gained access to a database could still run common passwords through the function to compare to the hashes in the table.

Additionally, hashed passwords are not unique to themselves due to the deterministic nature of hash function: when given the same input, the same output is always produced. If Alice and Bob both choose `dontpwnme4` as a password, their hash would be the same: `4420d1918bbcf7686defdf9560bb5087d20076de5f77b7cb4c3b40bf46ec428b`

The attacker can better predict the password that legitimate maps to that hash. Once the password is known, the same password can be used to access all the accounts that use that hash.

### Salting & Hashing Passwords
To mitigate this we salt the passwords. According to OWASP Guideliness, a **salt** is a fixed-length cryptographically-strong random value that is added to the input of hash functions to create unique hashes for every input, regardless of the input not being unique. A salt makes a hash function look non-deterministic, which is good as we don't want to reveal password duplications through our hashing.

Let’s say that we have password `farm1990M0O` and the salt `f1nd1ngn3m0`. We can salt that password by either appending or prepending the salt to it. For example: `farm1990M0Of1nd1ngn3m0` or `f1nd1ngn3m0farm1990M0O` are valid salted passwords. Once the salt is added, we can then hash it.

Alice and Bob decide to use both the same password, `farm1990M0O`. For Alice, we'll use `f1nd1ngn3m0` again as the salt. However, for Bob, we'll use `f1nd1ngd0ry` as the salt:

```
User: Alice
Password: farm1990M0O
Salt: f1nd1ngn3m0
Salted input: farm1990M0Of1nd1ngn3m0
Hash (SHA-256): 07dbb6e6832da0841dd79701200e4b179f1a94a7b3dd26f612817f3c03117434

User: Bob
Password: farm1990M0O
Salt: f1nd1ngd0ry
Salted input: farm1990M0Of1nd1ngd0ry
Hash (SHA-256): 11c150eb6c1b776f390be60a0a5933a2a2f8c0a0ce766ed92fea5bfd9313c8f6
```

Different users, same password. Different salts, different hashes. If someone looked at the full list of password hashes, no one would be able to tell that Alice and Bob both use the same password.

### Database Leakage
**Database leakage** refers to any information that is unintentionally released from a database. One example might be a password reset page, where a user can enter the e-mail to get a link to reset a password. If the site has one message for a sent e-mail and another message for an e-mail that’s not tied to an account, then users can figure out if an e-mail is associated with an account. This information, even if it doesn’t compromise the account, might still be sensitive.

**Therefore:**
- Make sure to Hash the passwords (aka. never store them plaintext)
    * Additionally, make sure to Salt the passwords before Hashing them, to prevent identical Hash formation from identical passwords that allow for various dictionary attacks
- Make sure your application does not leak unecessary data that might reveal and identify something