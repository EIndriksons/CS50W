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

