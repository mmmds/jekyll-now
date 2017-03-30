---
published: true
---
In this exercise we have credit card checking web application to exploit. It takes cred card number and returns information whether our card is compromised or not.

This VM can be found [here](https://pentesterlab.com/exercises/luhn).

Firstly, I tried to understand behavior of checking mechanism. I noticed that number has to be valid according to [Luhn algorithm](https://en.wikipedia.org/wiki/Luhn_algorithm). For valid number (eg. _88888888_) I received "_We have no information that your CC has been compromised._" and for invalid (eg. _12345_)  "_Invalid CC_". 

Behavior for odd number of digits is a little bit more complex. Application adds _0_ at the end of the number, so for example both _5432193_ and _54321930_ works. I also found that every non digit character is just ignored by the validation mechanism, for example "_a 5 b 4 x 321!!930_" is still a valid number. 

I assumed that when number is valid, application looks for it in database. I started to look for SQL Injection. I used what I had learned about this mechanism and prepared number "_543219' or 3!=0 --_". For validator it was still valid _54321930_ but for SQL query the whole input was taken and I received "_Your CC has been compromised :/_".
![]({{site.baseurl}}/images/luhn_1.png)

Obviously, it was blind SQL injection which I had to exploit. I handcrafted my own [script](https://github.com/mmmds/walkthroughs/blob/master/luhn/blind_luhn.py) to dump database. It uses Luhn algorithm to make sure that prepared query will be successfully validated on server side.
Key is stored in database.
![]({{site.baseurl}}/images/luhn_2.png)
