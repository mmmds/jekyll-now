---
published: true
---
Our goal is to gain access to admin account by padding oracle attack. Application allows us to register accounts then encrypted cookie is used to authenticate.

1. I started with analyzing length of blocks. I registered many accounts increasing their names length. I wrote the script which registers accounts and displays authentication cookie and its size. Starting from 1 character and ending with 20 characters I noticed that block size is 8 bytes.
![]({{site.baseurl}}/images/padding_oracle_register.png)

2. When I knew length of each block I was able to start decrypting cookies. I wrote another script which takes authentication cookie and decrypts every block except the first one.   
![]({{site.baseurl}}/images/padding_oracle_decrypt.png)
This result was what I had expected. Cookie contained username, probably without any other authentication information.

3. What I already learned gave me an idea to register an account using some username very similar to 'admin' (with one character changed - 'bdmin' in my case) and change it into 'admin'. I wrote last script which takes 'bdmin' cookie and modifies first character until valid admin cookie is generated.
![]({{site.baseurl}}/images/padding_oracle_modify.png)

4. In my browser I replaced authentication cookie with the new one. As the result I gained access to admin account.
![]({{site.baseurl}}/images/padding_oracle_admin.png)