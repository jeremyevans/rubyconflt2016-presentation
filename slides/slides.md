!SLIDE
.notes Hello everyone.  I'm excited and honored to be speaking here at RubyConf LT.|My presentation today is entitled Rodauth: Website Security Through Database Security, and I will discuss security issues related to website authentication, and how Rodauth is designed to handle these issues.

<img src="../../file/rodauth.svg" width="300" style="position: absolute; top: 120px; left: 100px;"/>
<h1 class="white" style="font-size: 100px; position: absolute; top: 200px; left: 400px;">Website Security</h1>
<h1 style="font-size: 50px; position: absolute; top: 300px; left: 650px;">Through</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 330px; left: 400px;">Database Security</h1>
<h1 style="font-size: 70px; position: absolute; bottom: 10px; right: 40px; text-align: right;">RubyConf LT 2016</h1>

!SLIDE bg-jeremy
.notes My name is Jeremy Evans, and I'm the maintainer of numerous ruby libraries,

<h1 class="white" style="font-size: 60px; position: absolute; top: 610px; left: 380px;">GitHub: <span class="mono">jeremyevans</span></h1>
<h1 class="white" style="font-size: 60px; position: absolute; top: 660px; left: 380px;">Twitter: <span class="mono">@jeremyevans0</span></h1>


!SLIDE
.notes the most popular of which is Sequel, the database toolkit for Ruby.

<img src="../../file/ruby-sequel.png" width="600" style="margin-top: 200px;"/>


!SLIDE bg-roda
.notes For the last couple years, I've also been working on Roda, which is a full featured toolkit for building maintainable and fast web applications in ruby.

<h1 class="roda white" style="font-size: 130px; position: absolute; top: 610px; left: 460px;">Roda</h1>


!SLIDE
.notes As most of you know, Ruby's most popular web framework is Rails. One of the disadvantages to using a ruby web framework that is not Rails, is that so many libraries are built to work just with Rails.

<img src="../../file/rails-logo.svg" width="600" style="margin-top: 200px;"/>


!SLIDE
.notes If you are looking for a full featured authentication framework, and you are using Rails, there is Devise, Authlogic, and Sorcery.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Devise</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Authlogic</h1>
<h1 style="font-size: 130px; position: absolute; top: 510px; left: 560px;">Sorcery</h1>

!SLIDE 
.notes In the past, I've worked mostly on non-Rails applications, and as there was not a good authentication library I could use, I ended up implementing authentication differently in each of the applications I worked on, based on the needs of the application.|Last year, after looking for existing solutions and not finding any that would work for all of my applications, I decided to build an authentication framework, which I named Rodauth.

<img src="../../file/rodauth.svg" width="600" style="position: absolute; top: 50px; left: 300px;"/>

!SLIDE 
.notes As I was developing Rodauth, one of the main design goals was that it should be flexible, as I had many applications with different authentication requirements.|I had applications where password hashes were stored in the same table as the logins, applications that authenticated against LDAP, applications needing separate authentication for separate groups of users, and applications that used database functions for authentication.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Flexible</h1>

!SLIDE 
.notes Another design goal was the default settings should be as secure as possible, and specifically make it difficult for an attacker to get access to password hashes.  I will talk a bit later about why this is so important.

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Secure</h1>

!SLIDE 
.notes The final design goal is that Rodauth should be simple to use and configure.  One of the most common security issues in applications is misconfiguration, and making configuration as simple as possible leads to more secure applications.

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 560px;">Simple</h1>

!SLIDE 
.notes I also wanted Rodauth to be full featured.  I wanted to be able to start a new application and with a few lines of code enable not just login authentication, but account creation and verification, password changing and resets, and brute force attack protection.|Other than the Rails-dependent authentication frameworks, most other ruby authentication libraries only offer login authentication, leaving you to implement the other features yourself.|So that is the history behind Rodauth and its design goals.

<h1 class="white" style="font-size: 140px; position: absolute; top: 120px; right: 195px;">Full</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; right: 160px;">Featured</h1>

!SLIDE skip
.notes I am first going to talk about how Rodauth attempts to protect against password hash cracking, stopping attackers from using password hashes stored on your site to break into other sites.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 70px;">Protect</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Against</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 320px; left: 310px;">Password</h1>
<h1 style="font-size: 100px; position: absolute; top: 400px; left: 360px;">Cracking</h1>

!SLIDE skip
.notes I will explain how Rodauth protects from the reverse, where another site has been attacked and its password hashes have been cracked, and attackers are attempting to use the cracked passwords to break into accounts on your site.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 70px;">Protect</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Against</h1>
<h1 style="font-size: 100px; position: absolute; top: 320px; left: 310px;">Cracked</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 400px; left: 360px;">Passwords</h1>

!SLIDE skip
.notes I will then talk about how Rodauth handles tokens used for things like password resets and account verification.

<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; right: 160px;">Token</h1>
<h1 style="font-size: 100px; position: absolute; top: 520px; right: 140px;">Security</h1>

!SLIDE skip
.notes I will talk briefly about the features Rodauth offers,

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 160px;">Features</h1>

!SLIDE skip
.notes and then talk about Rodauth's configuration DSL and the flexibility it offers to handle most authentication requirements, as well as some interesting aspects of Rodauth's internals.

<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Configuration</h1>

!SLIDE skip
.notes Finally, I will talk about how Rodauth can be integrated into existing systems.  It does not matter if you are using Rails, Sinatra, Roda, Hanami, or are running on bare Rack, you can use Rodauth in your application.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 360px;">Integration</h1>

!SLIDE
.notes I am now going to talk about what makes Rodauth different from other authentication libraries from a security perspective, which is the attempt to protect against password hash cracking by the use of multiple database accounts and database functions for authentication by default. This makes it difficult for attackers to even get access to the underlying password hashes.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 70px;">Protect</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Against</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 320px; left: 310px;">Password</h1>
<h1 style="font-size: 100px; position: absolute; top: 400px; left: 360px;">Cracking</h1>

!SLIDE
.notes First, why is access to password hashes so important.  Well, it stems from the fact that most humans are bad at remembering good passwords. This manifests itself in two ways.

<h1 style="font-size: 330px; position: absolute; top: 210px; left: 260px;">Why<span class="white">?</span></h1>

!SLIDE
.notes The first way is that humans tend to choose simple passwords. If you try to force them to use complex passwords, many humans will pick a simple password that meets your complexity requirements.

<h1 class="white" style="font-size: 100px; position: absolute; top: 140px; right: 270px;">Simple</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; right: 160px;">Passwords</h1>

!SLIDE skip
.notes If you do not have any requirements on passwords, many humans will use passwords like 123456 or password

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">123456</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 160px;">password</h1>

!SLIDE skip
.notes If you require that passwords must be at least eight characters and contain both uppercase letters, lowercase letters, and numbers, many humans will use passwords like Password1.  This is not significantly more secure than just password by itself.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">123456</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 160px;">password</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 510px; left: 160px;">Password1</h1>

!SLIDE skip
.notes Password complexity requirements only make it more likely that humans will write down the password. If a human will not use a secure password in the absense of password complexity requirements, it is unlikely they will use a secure password if you have password complexity requirements.

<h1 style="font-size: 130px; position: absolute; top: 210px; left: 260px;">Password</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 320px; left: 290px;">Complexity</h1>
<h1 style="font-size: 130px; position: absolute; top: 400px; left: 160px;">Requirements</h1>

!SLIDE
.notes The second way is that humans tend to reuse passwords on multiple sites.  It is impractical to attempt to prevent this.

<h1 style="font-size: 100px; position: absolute; top: 110px; right: 160px;">Password</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 160px; right: 340px;">Reuse</h1>

!SLIDE
.notes Even if you are not storing any important data in your own system, if you are storing password hashes for users, and your application gets compromised, attackers can try to crack the password hashes stored in your system, and use the passwords to access other systems.

<h1 style="font-size: 200px; position: absolute; top: 210px; left: 160px;">Attack</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 210px; left: 760px;">Other</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 310px; left: 760px;">Sites</h1>

!SLIDE
.notes In order for an attacker to use password hashes from one website to attack another website, the first thing the attacker needs to do is get access to the password hashes.  Without access to the password hashes, there is nothing the attacker can use to attack other sites.  So as much as possible, you should make it difficult for an attacker to access the password hashes.

<h1 class="white" style="font-size: 100px; position: absolute; top: 160px; left: 260px;">Restrict</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Password</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 320px; left: 240px;">Hash</h1>
<h1 style="font-size: 130px; position: absolute; top: 370px; left: 160px;">Access</h1>

!SLIDE
.notes Before I discuss how Rodauth protects against access to the password hashes, let me first discuss the worst case scenario, where an attacker is able to access the password hashes. The first thing they will probably do is to attempt to crack the hashes.

<h1 class="white" style="font-size: 160px; position: absolute; top: 310px; left: 260px;">Cracking</h1>

!SLIDE skip
.notes This is generally done first using a dictionary attack.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Dictionary</h1>
<h1 style="font-size: 100px; position: absolute; top: 230px; left: 460px;">Attack</h1>

!SLIDE skip
.notes A dictionary attack basically tries each word in a dictionary, in order.  In English, there are only a few thousand words in a dictionary, so any password that is also a word that appears in a dictionary will be cracked almost immediately.  There many variations on dictionary attacks.

<pre style="font-size: 60px; margin-top: 10px;"><code>aardvark
aardwolf
aback
...
</code></pre>

!SLIDE skip
.notes One variation on a dictionary attack uses each dictionary word as a prefix, and checks different suffixes.  Passwords that use simple additions like this will also be cracked quickly.

<pre style="font-size: 60px; margin-top: 10px;"><code>aardvark<b>1</b> 
aardvark<b>2</b>
...
aardvark<b>9</b>
aardwolf<b>1</b>
aardwolf<b>2</b>
...
</code></pre>

!SLIDE skip
.notes Another uses common substitutions of symbols and numbers for letters.  Passwords that use substitutions like this also tend to be cracked quickly.

<pre style="font-size: 60px; margin-top: 10px;"><code><b>@</b>ardvark
a<b>@</b>rdvark
aardv<b>@</b>rk
...
<b>@</b>ardwolf
a<b>@</b>rdwolf
aardw<b>0</b>lf
aardwo<b>1</b>f
...
</code></pre>

!SLIDE skip
.notes The next attack is usually a brute force attack.

<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Brute</h1>
<h1 style="font-size: 130px; position: absolute; top: 330px; left: 160px;">Force</h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 240px; left: 520px;">Attack</h1>

!SLIDE skip
.notes A brute force attack basically tries all possible combinations of characters, and will eventually be able to crack any password given enough time.

<pre style="font-size: 60px; margin-top: 10px;"><code>aaaa
aaab
...
zzzy
zzzz
aaaaa
aaaab
...
zzzzz
aaaaaa
</code></pre>

!SLIDE
.notes The amount of time it takes on average to crack a password hash via brute force depends on the length of the password and the character set it uses, as that decides the universe of possible passwords.

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 160px;">Combinations</h1>

!SLIDE
.notes Here is a table showing how many combinations there are for passwords of a given length and character set.

<pre style="font-size: 60px; margin-top: 10px;"><code>
[a-z]{4}       | 456976
[a-z]{8}       | 2 * 10<sup>11</sup>
[a-z0-9]{8}    | 3 * 10<sup>12</sup>
[A-Za-z]{8}    | 5 * 10<sup>13</sup>
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup>
[a-z]{11}      | 3 * 10<sup>15</sup>
</code></pre>

!SLIDE
.notes There are fewer than a half million 4 character all lowercase passwords

<pre style="font-size: 60px; margin-top: 10px;"><code>
[a-z]{4}       | <b>456976</b>
[a-z]{8}       | 2 * 10<sup>11</sup>
[a-z0-9]{8}    | 3 * 10<sup>12</sup>
[A-Za-z]{8}    | 5 * 10<sup>13</sup>
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup>
[a-z]{11}      | 3 * 10<sup>15</sup>
</code></pre>

!SLIDE
.notes but more than 208 billion possible 8 character all lowercase passwords

<pre style="font-size: 60px; margin-top: 10px;"><code>
[a-z]{4}       | 456976
[a-z]{8}       | <b>2 * 10<sup>11</sup></b>
[a-z0-9]{8}    | 3 * 10<sup>12</sup>
[A-Za-z]{8}    | 5 * 10<sup>13</sup>
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup>
[a-z]{11}      | 3 * 10<sup>15</sup>
</code></pre>

!SLIDE
.notes and more than 3 quadrillion 11 character all lowercase passwords.

<pre style="font-size: 60px; margin-top: 10px;"><code>
[a-z]{4}       | 456976
[a-z]{8}       | 2 * 10<sup>11</sup>
[a-z0-9]{8}    | 3 * 10<sup>12</sup>
[A-Za-z]{8}    | 5 * 10<sup>13</sup>
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup>
[a-z]{11}      | <b>3 * 10<sup>15</sup></b>
</code></pre>

!SLIDE
.notes One thing to note here is that less complex but longer passwords are in general more secure than more complex but shorter passwords.  An 11 character all lower case password is about 16 times as secure as an 8 character password with uppercase letters, lowercase letters, and numbers.

<pre style="font-size: 60px; margin-top: 10px;"><code>
[a-z]{4}       | 456976
[a-z]{8}       | 2 * 10<sup>11</sup>
[a-z0-9]{8}    | 3 * 10<sup>12</sup>
[A-Za-z]{8}    | 5 * 10<sup>13</sup>
<b>[A-Za-z0-9]{8} | 2 * 10<sup>14</sup></b>
<b>[a-z]{11}      | 3 * 10<sup>15</sup> 16x</b> 
</code></pre>

!SLIDE
.notes You can use that information to choose better passwords for your own accounts. As I mentioned earlier, you cannot force humans to use good passwords.  However, you can choose which password hash algorithm to use, and that can have a dramatic effect on password hash cracking times.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Password</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 230px; left: 260px;">Hash</h1>
<h1 style="font-size: 150px; position: absolute; top: 290px; left: 120px;">Algorithms</h1>

!SLIDE
.notes Many of you are probably familar with the SHA1 and SHA256 hash algorithms.  While these are decent cryptographic hash algorithms, they are bad password hash algorithms.|First, these are designed for speed, and you generally want password hashing to be slow, not fast, in order to make cracking the password hashes more difficult.|Second, these hashes do not include salts, which means that when using them for password hashing, the same password always results in the same hash.

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 160px;">SHA1</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 430px; left: 160px;">SHA256</h1>

!SLIDE 
.notes If you take the SHA1 hash of the word password, it always results in the same hash.

<pre style="font-size: 50px; margin-top: 10px;"><code>
$ echo -n password | sha1
<b>5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8</b>

$ echo -n password | sha1
<b>5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8</b>
</code></pre>

!SLIDE
.notes That is bad from a security perspective, as it makes it possible to crack the password hashes using a precomputed rainbow table, allowing all simple passwords to be cracked immediately.|A rainbow table of all 9 character alphanumeric SHA1 password hashes takes only 690GB of storage, and would allow immediate cracking of any of these passwords hashes.

<h1 style="font-size: 100px; position: absolute; top: 240px; left: 110px;">Rainbow</h1>
<h1 style="font-size: 130px; position: absolute; top: 330px; left: 160px;">Table</h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 250px; left: 520px;">Attack</h1>

!SLIDE
.notes To prevent a rainbow table attack, you do what is called salting, where you add random data before the password, and when you check a password, you prepend the salt to the password and check if the resulting hash matches.

<h1 class="white" style="font-size: 200px; position: absolute; top: 310px; left: 360px;">Salting</h1>

!SLIDE
.notes In addition to salting, the other way to increase password security is to require more computation to create the hash.  Good password hash algorithms use many iterations of an underlying algorithm internally, which is referred to as streching.

<h1 style="font-size: 200px; position: absolute; top: 110px; left: 260px;">Stretching</h1>

!SLIDE
.notes These days, there are two good options for password hashes.  One is bcrypt, another is scrypt.  bcrypt is older and has been studied more, scrypt is newer and takes more memory in addition to a lot of CPU time, making it theoretically more resistant to some attacks using custom hardware.  Rodauth currently uses bcrypt.

<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 460px;">bcrypt</h1>
<h1 style="font-size: 130px; position: absolute; top: 430px; left: 460px;">scrypt</h1>

!SLIDE
.notes If you take the bcrypt hash of the word password, it results in different output, due to the inclusion of a random salt.

<pre style="font-size: 32px; margin-top: 210px;"><code>$ echo -n password | encrypt
<b>$2b$08$tO1zyO2F8wRwISMvDg.YCuLUPoMDGwVPpl76vf5bXng3E4bRRCoui</b>

$ echo -n password | encrypt
<b>$2b$08$WdUcdTDMVgTNUFeQb/kWku7hfAf9R0JcHzwSb90NQPIpbF7tqiojO</b>
</code></pre>

!SLIDE
.notes The highlighted part here is the salt for the password, with the remaining part being the hash itself. So to check a bcrypt password for validity, it first prepends the salt to the provided password, then it runs the bcrypt algorithm over the result, and then checks if the resulting hash matches.

<pre style="font-size: 32px; margin-top: 210px;"><code>$ echo -n password | encrypt
<b>$2b$08$tO1zyO2F8wRwISMvDg.YCu</b>LUPoMDGwVPpl76vf5bXng3E4bRRCoui

$ echo -n password | encrypt
<b>$2b$08$WdUcdTDMVgTNUFeQb/kWku</b>7hfAf9R0JcHzwSb90NQPIpbF7tqiojO
</code></pre>

!SLIDE
.notes Bcrypt includes a cost factor as part of the salt, which each increase in the cost factor doubling the amount of work bcrypt will perform to compute the hash.  In this case, the cost factor is 8.  I'll use the term bcrypt(8) to refer to bcrypt with cost factor 8.

<pre style="font-size: 32px; margin-top: 210px;"><code>$ echo -n password | encrypt
$2b$<b>08</b>$tO1zyO2F8wRwISMvDg.YCuLUPoMDGwVPpl76vf5bXng3E4bRRCoui
</code></pre>

!SLIDE
.notes One of my test machines, which is fairly old, can compute 7 million SHA1 password hashes per second.  It can compute 80 bcrypt(8) password hashes per second.  So bcrypt(8) is almost 100,000 times harder to crack than SHA1.

<h1 style="font-size: 130px; position: absolute; top: 210px; left: 360px;">Time</h1>
<h1 class="white" style="font-size: 70px; position: absolute; top: 330px; left: 500px;">to</h1>
<h1 style="font-size: 130px; position: absolute; top: 360px; left: 360px;">Crack</h1>

!SLIDE
.notes If we consider the time to check each password hash by the number of possible passwords, we can compare about how long it would take to crack the average password in each of these sets, depending on whether the hashes use SHA1 or bcrypt(8).

<pre style="font-size: 36px; margin-top: 10px;"><code>set            |    # <sup>  </sup>  |  SHA1      | bcrypt(8)
[a-z]{4}       | 456976<sup>  </sup> | &lt;1 second  | 1.5 hours
[a-z]{8}       | 2 * 10<sup>11</sup> |  8 hours   | 82 years
[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;2 days    | 1 millenium
[A-Za-z]{8}    | 5 * 10<sup>13</sup> | 88 days    | 21 millenia
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | &lt;1 year    | 86 millenia
[a-z]{11}      | 3 * 10<sup>15</sup> | 16 years   | 1454 millenia
</code></pre>

!SLIDE
.notes One thing we can see is that it takes more time to crack an 8 character password hashed with bcrypt(8) than it takes to crack an 11 character password hashed with SHA1.|Note that these numbers were for a older single machine. Anyone serious about cracking password hashes could easily spin up tens of thousands of more powerful cloud machines dedicated to cracking.|Let us see what the numbers look like for an serious attacker.

<pre style="font-size: 36px; margin-top: 10px;"><code>set            |    # <sup>  </sup>  |  SHA1      | bcrypt(8)
[a-z]{4}       | 456976<sup>  </sup> | &lt;1 second  | 1.5 hours
[a-z]{8}       | 2 * 10<sup>11</sup> |  8 hours   | <b>82 years</b>
[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;2 days    | 1 millenium
[A-Za-z]{8}    | 5 * 10<sup>13</sup> | 88 days    | 21 millenia
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | &lt;1 year    | 86 millenia
[a-z]{11}      | 3 * 10<sup>15</sup> | <b>16 years</b>   | 1454 millenia
</code></pre>

!SLIDE
.notes As you can see, against a serious attacker, all simple SHA1 password hashes can be cracked almost immediately, and most simple bcrypt passwords could be cracked if the attacker is willing to wait.|Note that the numbers for SHA1 here are not even considering the use of graphics processors.  SHA1 hashes can be cracked up to 500 times faster on a graphics processor than on a CPU.  However, graphics processors are slower than CPUs for cracking bcrypt hashes, due to bcrypt's design, which requires constant memory access.  This is another reason to use bcrypt over SHA1.

<pre style="font-size: 36px; margin-top: 10px;"><code>set            |    # <sup>  </sup>  |  SHA1      | bcrypt(8)
[a-z]{8}       | 2 * 10<sup>11</sup> | &lt;1 second  | 43 minutes
[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;1 second  | 9 hours
[A-Za-z]{8}    | 5 * 10<sup>13</sup> |  7 seconds | 8 days
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | 30 seconds | 31 days
[a-z]{11}      | 3 * 10<sup>15</sup> |  9 minutes | 1.5 years
</code></pre>

!SLIDE
.notes I should also point out that these numbers are for bcrypt(8), where Rodauth's default is bcrypt(10), which is 4 times harder to crack.

<pre style="font-size: 36px; margin-top: 10px;"><code>set            |    # <sup>  </sup>  |  SHA1      | bcrypt<b>(8)</b>
[a-z]{8}       | 2 * 10<sup>11</sup> | &lt;1 second  | 43 minutes
[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;1 second  | 9 hours
[A-Za-z]{8}    | 5 * 10<sup>13</sup> |  7 seconds | 8 days
[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | 30 seconds | 31 days
[a-z]{11}      | 3 * 10<sup>15</sup> |  9 minutes | 1.5 years
</code></pre>

!SLIDE
.notes Now we can answer the question of why protecting against access to password hashes is so important.  It is because if attackers get access to the password hashes, they can crack the hashes to get the passwords, and then they can use the passwords to attack other sites.  So protecting against access to password hashes is critical from a security perspective.

<h1 style="font-size: 330px; position: absolute; top: 210px; left: 260px;">Why<span class="white">?</span></h1>

!SLIDE
.notes How do you prevent an attacker from accessing the password hashes, while still checking passwords for validity during login?

<h1 style="font-size: 330px; position: absolute; top: 210px; left: 260px;"><span class="white">How</span>?</h1>

!SLIDE
.notes Rodauth's approach is store password hashes in a separate table.

<h1 class="white" style="font-size: 100px; position: absolute; top: 310px; left: 260px;">Separate</h1>
<h1 style="font-size: 130px; position: absolute; top: 400px; left: 260px;">Tables</h1>

!SLIDE
.notes Rodauth by default uses the accounts table to store accounts

<pre style="font-size: 40px; margin-top: 10px;"><code>
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);
</code></pre>

!SLIDE
.notes and the account_password_hashes table to store password hashes.

<pre style="font-size: 40px; margin-top: 10px;"><code>
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);


CREATE TABLE account_password_hashes (
id integer PRIMARY KEY REFERENCES accounts,
password_hash text NOT NULL
);
</code></pre>

!SLIDE
.notes The account_password_hashes table has a foreign key reference to the accounts table that is also the primary key, ensuring there is only a single password hash for each account.

<pre style="font-size: 40px; margin-top: 10px;"><code>
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);


CREATE TABLE account_password_hashes (
<b>id integer PRIMARY KEY REFERENCES accounts,</b>
password_hash text NOT NULL
);
</code></pre>

!SLIDE
.notes Rodauth uses two separate database accounts.

<h1 class="white" style="font-size: 230px; position: absolute; top: 280px; left: 140px;">2</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 260px;">Database</h1>
<h1 style="font-size: 130px; position: absolute; top: 410px; left: 260px;">Accounts</h1>

!SLIDE
.notes One database account is used by the application itself, and has access to most of the tables, including the accounts table. For purposes of this presentation, I will call this account app.

<pre style="font-size: 40px; margin-top: 10px;"><code><b>-- Owned by application database account (app)</b>
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);


CREATE TABLE account_password_hashes (
id integer PRIMARY KEY REFERENCES accounts,
password_hash text NOT NULL
);
</code></pre>

!SLIDE
.notes A separate database account owns the password hash table.  I will refer to this account as ph.

<pre style="font-size: 40px; margin-top: 10px;"><code>-- Owned by application database account (app)
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);

<b>-- Owned by password hash database account (ph)</b>
CREATE TABLE account_password_hashes (
id integer PRIMARY KEY REFERENCES accounts,
password_hash text NOT NULL
);
</code></pre>

!SLIDE
.notes After the ph account creates the password hash table, it revokes access to the password hash table from all users.

<pre style="font-size: 40px; margin-top: 10px;"><code>-- Owned by application database account (app)>
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);

-- Owned by password hash database account (ph)
CREATE TABLE account_password_hashes (
id integer PRIMARY KEY REFERENCES accounts,
password_hash text NOT NULL
);
<b>REVOKE ALL ON account_password_hashes FROM public;</b>
</code></pre>

!SLIDE
.notes Then the ph account grants access to insert, update, and delete password hashes to the app account.  This allows the app account to easily insert password hashes for new users, remove password hashes for deleted users, and change password hashes for existing users. However, it does not allow the app account access to read the password hash table.

<pre style="font-size: 40px; margin-top: 10px;"><code>-- Owned by application database account (app)>
CREATE TABLE accounts (
id integer PRIMARY KEY,
email text NOT NULL
);

-- Owned by password hash database account (ph)
CREATE TABLE account_password_hashes (
id integer PRIMARY KEY REFERENCES accounts,
password_hash text NOT NULL
);
REVOKE ALL ON account_password_hashes FROM public;
<b>GRANT INSERT, UPDATE, DELETE ON</b>
      <b>account_password_hashes TO app;</b>
</code></pre>


!SLIDE
.notes In order to authenticate without allowing access to the hashes, Rodauth uses two database functions.  Both of the database functions are created by the ph account.

<h1 style="font-size: 230px; position: absolute; top: 280px; left: 140px;">2</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 260px;">Database</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; left: 260px;">Functions</h1>


!SLIDE
.notes The first function is called rodauth_get_salt, which returns the password salt.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_get_salt(account_id integer)
RETURNS text AS $$
DECLARE salt text;
BEGIN
SELECT substr(password_hash, 0, 30) INTO salt 
FROM account_password_hashes
WHERE account_id = id;
RETURN salt;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes This function takes the account id as an integer.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_get_salt(<b>account_id integer</b>)
RETURNS text AS $$
DECLARE salt text;
BEGIN
SELECT substr(password_hash, 0, 30) INTO salt 
FROM account_password_hashes
WHERE account_id = id;
RETURN salt;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes It finds the row with the matching id.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_get_salt(account_id integer)
RETURNS text AS $$
DECLARE salt text;
BEGIN
SELECT substr(password_hash, 0, 30) INTO salt 
FROM account_password_hashes
WHERE <b>account_id = id</b>;
RETURN salt;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes The account_password_hashes table stores the bcrypt password hash in a single column.  So in order to get the salt, it needs to extract the salt portion of the hash, using the substring function.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_get_salt(account_id integer)
RETURNS text AS $$
DECLARE salt text;
BEGIN
SELECT <b>substr(password_hash, 0, 30)</b> INTO salt 
FROM account_password_hashes
WHERE account_id = id;
RETURN salt;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes One important part is the use of SECURITY DEFINER when creating the function.  Setting SECURITY DEFINER for a database function is similar to setting an executable as setuid in Unix, in that the function executes using the permissions of the user that defined the function, instead of the user that is executing the function.|This is what allows the app account to check passwords for validity.  While the app account cannot read the password hashes, it can call this function, which runs as the ph account and can read the password hashes.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_get_salt(account_id integer)
RETURNS text AS $$
DECLARE salt text;
BEGIN
SELECT substr(password_hash, 0, 30) INTO salt 
FROM account_password_hashes
WHERE account_id = id;
RETURN salt;
END;
$$ LANGUAGE plpgsql
<b>SECURITY DEFINER</b>
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes After the application retrieves the salt for the account, it uses the password provided by the user to construct the hash.  Then it needs to call a database function to check if that hash is valid.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Application</h1>
<h1 style="font-size: 100px; position: absolute; top: 230px; left: 380px;">Computes</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 310px; left: 460px;">Password</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 390px; left: 500px;">Hash</h1>

!SLIDE
.notes That function is rodauth_valid_password_hash.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_valid_password_hash
(account_id integer, hash text)
RETURNS boolean AS $$
DECLARE valid boolean;
BEGIN
SELECT password_hash = hash INTO valid 
FROM account_password_hashes
WHERE account_id = id;
RETURN valid;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes This function takes the account id and the password hash computed by the application.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_valid_password_hash
(<b>account_id integer, hash text</b>)
RETURNS boolean AS $$
DECLARE valid boolean;
BEGIN
SELECT password_hash = hash INTO valid 
FROM account_password_hashes
WHERE account_id = id;
RETURN valid;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes It finds the row with the matching id.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_valid_password_hash
(account_id integer, hash text)
RETURNS boolean AS $$
DECLARE valid boolean;
BEGIN
SELECT password_hash = hash INTO valid 
FROM account_password_hashes
WHERE <b>account_id = id</b>;
RETURN valid;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes And it returns whether the password hash computed by the application matches the stored password hash.  If so, the user provided the correct password and should be allowed to login.  Otherwise, the user provided an incorrect password and should not be allowed to login.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_valid_password_hash
(account_id integer, hash text)
RETURNS boolean AS $$
DECLARE valid boolean;
BEGIN
SELECT <b>password_hash = hash</b> INTO valid 
FROM account_password_hashes
WHERE account_id = id;
RETURN valid;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes This approach of disallowing the application access to read password hashes offers protections against two types of attacks.

<h1 class="white" style="font-size: 150px; position: absolute; top: 310px; left: 260px;">Protections</h1>

!SLIDE
.notes The most common way that attackers get access to password hashes is by exploiting SQL injection vulnerabilities.  With Rodauth's approach, an attacker cannot get access to the password hash table via an SQL injection vulnerability.

<h1 class="white" style="font-size: 200px; position: absolute; top: 110px; right: 160px;">SQL</h1>
<h1 style="font-size: 130px; position: absolute; top: 290px; right: 260px;">Injection</h1>

!SLIDE
.notes What happens if an attacker tries to read all password hashes directly?  This simple SELECT query to read the password hash table

<pre style="font-size: 30px; margin-top: 210px;"><code>SELECT * FROM account_password_hashes;
</code></pre>


!SLIDE
.notes results in an error, since the app account does not have access to this table.

<pre style="font-size: 30px; margin-top: 210px;"><code>SELECT * FROM account_password_hashes;
<b>ERROR: permission denied for relation account_password_hashes</b>
</code></pre>


!SLIDE skip
.notes More sophisticated attackers might try to grant themselves access to the table first.

<pre style="font-size: 30px; margin-top: 210px;"><code>GRANT SELECT ON account_password_hashes to my_account;
</code></pre>

!SLIDE skip
.notes While this does not result in an error, it also does not make any changes.  The reason for this is that the app account is not the owner of the password hash table, and only the owner of the table or a superuser can grant privileges on the table.

<pre style="font-size: 30px; margin-top: 210px;"><code>GRANT SELECT ON account_password_hashes to my_account;
<b>WARNING:  no privileges were granted for "account_password_hashes"</b>
</code></pre>


!SLIDE skip
.notes Maybe an attacker will try to change the owner of the table?

<pre style="font-size: 30px; margin-top: 210px;"><code>ALTER TABLE account_password_hashes OWNER TO my_account;
</code></pre>

!SLIDE skip
.notes This also raises an error, as you cannot change the owner of a table unless you are the owner of the table.

<pre style="font-size: 30px; margin-top: 210px;"><code>ALTER TABLE account_password_hashes OWNER TO my_account;
<b>ERROR:  must be owner of relation account_password_hashes</b>
</code></pre>

!SLIDE skip
.notes Basically, Rodauth's approach is secure against SQL Injection.

<h1 style="font-size: 130px; position: absolute; top: 40px; left: 290px;">Secure</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 150px; left: 260px;">Against </h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 110px; right: 160px;">SQL</h1>
<h1 style="font-size: 130px; position: absolute; top: 290px; right: 260px;">Injection</h1>

!SLIDE
.notes What about a remote code execution vulnerability?  This is the worst type of vulnerability in an application, as the attacker can run arbitrary code.|The good news is Rodauth is secure against this type of attack on stored password hashes.  Unless the attacker can get access to either the ph account or a database superuser account, they cannot get access to the password hashes.

<h1 style="font-size: 100px; position: absolute; top: 260px; left: 450px;">Remote Code</h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 310px; left: 160px;">Execution</h1>

!SLIDE
.notes However, if an attacker can remotely execute code, while they may not be able to get access to stored password hashes, they can fairly easily compromise any account that logs in while they have control of the system.  They do not need the stored password hash in this case, as a user logging in provides the plain text of their password.

<h1 style="font-size: 100px; position: absolute; top: 160px; left: 260px;">Plain Text</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 210px; left: 160px;">Password</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 200px;">Access</h1>

!SLIDE skip
.notes If an attacker can exploit a remote code execution vulnerability in your application, they may be able to escalate their privileges to get access to a database superuser account, database files, or database backups.  To mitigate against that risk you need to isolate your database from your application, which is a bit out of scope for this talk.

<h1 style="font-size: 100px; position: absolute; top: 160px; left: 260px;">Privilege</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 240px; left: 210px;">Escalation</h1>

!SLIDE skip
.notes While an attacker that could exploit a remote code execution vulnerability would not have direct access to stored password hashes, there is a possible timing attack which may allow them to more easily guess password hashes.

<h1 class="white" style="font-size: 150px; position: absolute; top: 310px; left: 360px;">Timing</h1>
<h1 style="font-size: 130px; position: absolute; top: 430px; left: 610px;">Attack</h1>

!SLIDE skip
.notes If we go back to the database function used for checking password hashes, it just does a simple comparison of the existing hash to the provided hash.  This means that the time this query will take to return a result is in some way dependent on how many initial characters of the provided password hash match the stored password hash.|It is theoretically possible for attacker to exploit this to determine the stored password hash, though I expect such an attack would be difficult.  This type of attack could be mitigated by using a timing safe string comparison function, but unfortunately most databases do not provide one.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION
rodauth_valid_password_hash
(account_id integer, hash text)
RETURNS boolean AS $$
DECLARE valid boolean;
BEGIN
SELECT <b>password_hash = hash</b> INTO valid 
FROM account_password_hashes
WHERE account_id = id;
RETURN valid;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp;
</code></pre>

!SLIDE
.notes There are some limitations with Rodauth's approach of using multiple database accounts and database functions for authentication.  First, it requires that you have the ability to use multiple accounts in your database.  This is fairly easy if you control the database.

<h1 style="font-size: 200px; position: absolute; top: 410px; left: 160px;">Limitations</h1>

!SLIDE
.notes However, if you are using a service like Heroku to supply the database, you may not be able to use multiple database accounts.

<img src="../../file/heroku.svg" width="900" heigth="300" style="margin-top: 250px" />

!SLIDE
.notes Second, Rodauth only has built in support for setting up the necessary database authentication functions on PostgreSQL, MySQL, and Microsoft SQL Server .  The approach Rodauth uses should be portable to other databases that support multiple accounts and functions that execute with the permissions of the definer instead of the permissions of the executer.

<h1 class="white" style="font-size: 170px; position: absolute; top: 130px; left: 150px;">PostgreSQL</h1>
<h1 style="font-size: 70px; position: absolute; top: 285px; left: 390px;">&amp;</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 260px;">MySQL</h1>
<h1 style="font-size: 70px; position: absolute; top: 425px; left: 420px;">&amp;</h1>
<h1 class="white" style="font-size: 60px; position: absolute; top: 470px; left: 260px;">Microsoft SQL Server</h1>
<h1 style="font-size: 210px; position: absolute; top: 460px; left: 130px;">Supported</h1>

!SLIDE
.notes When you cannot use multiple database accounts, or when using a database that does not support the database authentication functions, Rodauth will access the password hash table directly, which drops the security level to roughly the same as other Ruby authentication frameworks.|Rodauth also supports storing the password hash in the same table as the account, allowing it to work with existing databases with that schema.

<h1 class="white" style="font-size: 100px; position: absolute; top: 260px; left: 570px;">Other</h1>
<h1 style="font-size: 130px; position: absolute; top: 320px; left: 260px;">Databases</h1>


!SLIDE
.notes In review, Rodauth protects against password hash access using multiple database accounts and database functions, and a secure password hash algorithm to slow attackers down if they do get access to the password hashes.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 70px;">Protect</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Against</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 320px; left: 310px;">Password</h1>
<h1 style="font-size: 100px; position: absolute; top: 400px; left: 360px;">Cracking</h1>
<div class="review">Review</div>

!SLIDE
.notes Protecting access to password hashes prevents attackers from using password hashes from your site to attack other sites.  How do you protect against the reverse, with attackers using passwords hashes from other sites to attack your site?

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 70px;">Protect</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Against</h1>
<h1 style="font-size: 100px; position: absolute; top: 320px; left: 310px;">Cracked</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 400px; left: 360px;">Passwords</h1>

!SLIDE
.notes A good way to prevent that is to require something in addition to the password when authenticating the user.  This is referred to as 2FA

<h1 class="white" style="font-size: 350px; position: absolute; top: 210px; left: 160px;">2</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">F</h1>
<h1 style="font-size: 130px; position: absolute; top: 420px; left: 360px;">A</h1>

!SLIDE
.notes or 2 factor authentication.  With 2 factor authentication, the first factor is the password, and users must also provide a second factor to authenticate.  This second factor can be a physical object, biometric, rotating pass code, or something else.


<h1 class="white" style="font-size: 350px; position: absolute; top: 210px; left: 160px;">2</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Factor</h1>
<h1 style="font-size: 130px; position: absolute; top: 420px; left: 360px;">Authentication</h1>

!SLIDE 
.notes One way to integrate 2 factor authentication into your application is to have users register their mobile phone number, and send SMS messages to that phone number containing codes that the user must enter.

<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; right: 160px;">SMS</h1>

!SLIDE 
.notes This does require all of your users have a working mobile phone on which to receive SMS codes in order to login, which can be problematic.

<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; right: 160px;">SMS</h1>
<h1 style="font-size: 70px; position: absolute; top: 510px; right: 180px;">Requires</h1>
<h1 style="font-size: 70px; position: absolute; top: 570px; right: 120px;">Mobile</h1>

!SLIDE
.notes One open standard for 2 factor authentication is U2F

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">U</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 230px; left: 160px;">2</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 160px;">F</h1>

!SLIDE
.notes or Universal 2nd Factor.  With U2F, the user must have a separate physical USB security key, as well as a compatible browser.  Logging in with U2F requires inserting the security key, and pressing a button on the security key after typing in the user name and password.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Universal</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 230px; left: 160px;">2nd</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 160px;">Factor</h1>

!SLIDE
.notes The main problem with U2F is it requires a separate security key.  Unlike mobile phones, users are not likely to already have a U2F-compatible security key, and are not as likely to keep it with them as they are a mobile phone.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Universal</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 230px; left: 160px;">2nd</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 160px;">Factor</h1>
<h1 style="font-size: 70px; position: absolute; top: 260px; left: 560px;">Requires</h1>
<h1 style="font-size: 70px; position: absolute; top: 330px; left: 560px;">USB Key</h1>

!SLIDE
.notes Additionally, only Google Chrome currently has native support for U2F, so users using other browsers cannot use U2F without a separate plugin or extension.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Universal</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 230px; left: 160px;">2nd</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 160px;">Factor</h1>
<h1 style="font-size: 70px; position: absolute; top: 260px; left: 560px;">Requires</h1>
<h1 style="font-size: 70px; position: absolute; top: 330px; left: 560px;">Chrome</h1>

!SLIDE
.notes Another open standard for 2 factor authentication is TOTP

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 160px;">T</h1>
<h1 style="font-size: 130px; position: absolute; top: 220px; left: 160px;">O</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 330px; left: 160px;">T</h1>
<h1 style="font-size: 130px; position: absolute; top: 440px; left: 160px;">P</h1>

!SLIDE
.notes which stands for Time-Based One-Time Password.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Time-Based</h1>
<h1 style="font-size: 130px; position: absolute; top: 220px; left: 160px;">One</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 330px; left: 160px;">Time</h1>
<h1 style="font-size: 130px; position: absolute; top: 440px; left: 160px;">Password</h1>

!SLIDE skip
.notes TOTP is described by RFC 6238, which was published in 2011, so it has been around for a while.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Time-Based</h1>
<h1 style="font-size: 130px; position: absolute; top: 220px; left: 160px;">One</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 330px; left: 160px;">Time</h1>
<h1 style="font-size: 130px; position: absolute; top: 440px; left: 160px;">Password</h1>
<h1 class="white" style="font-size: 70px; position: absolute; top: 560px; left: 360px;">RFC 6238</h1>

!SLIDE
.notes TOTP is time based. That means that as time passes, the authentication code to login changes.  Many TOTP implementations default to rotating the authentication code every 30 seconds.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Time-Based</h1>

!SLIDE skip
.notes The authentication code generated for this minute may be 52 64 61,

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 160px;">T1: 526461</h1>

!SLIDE skip
.notes but the following minute the authentication code will change to 75 74 68

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 160px;">T1: 526461</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 420px; left: 160px;">T2: 757468</h1>

!SLIDE
.notes TOTP works by having the client and server share a secret, which is usally displayed as a 16 character string.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 260px;">Shared</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 200px; left: 190px;">Secret</h1>

!SLIDE
.notes If you want to store the TOTP secret on your mobile phone, you can scan in a QR code containing the secret using the phone's camera.

<img src="../../file/rodauth_qrcode.svg" width="600" style="margin-top: 50px;"/>

!SLIDE
.notes The are many mobile apps that support TOTP, scanning in secrets via QR codes, and displaying the current authentication code for the site. Google Authenticator is probably the most popular app that supports TOTP.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; right: 300px;">Mobile</h1>
<h1 style="font-size: 130px; position: absolute; top: 200px; right: 270px;">Apps</h1>

!SLIDE
.notes However, a mobile phone is not required, there are plenty of other tools that handle TOTP.  There are many libraries, command line programs, and graphical programs that support TOTP.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; right: 300px;">Mobile</h1>
<h1 style="font-size: 130px; position: absolute; top: 200px; right: 270px;">Apps</h1>
<h1 class="white" style="font-size: 50px; position: absolute; top: 320px; right: 50px;">Not Required</h1>

!SLIDE
.notes TOTP just requires an additional form element when logging in, and works in all browsers.

<h1 class="white" style="font-size: 130px; position: absolute; top: 200px; left: 190px;">Supports</h1>
<h1 style="font-size: 150px; position: absolute; top: 310px; left: 360px;">All</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; left: 280px;">Browsers</h1>

!SLIDE
.notes Because TOTP relies on time and codes are changed often, it is dependent on the client's clock being synchronized with the server's clock.  However, this is not a problem in general as most computers and mobile phones synchronize to the same root time source via network time protocol.

<h1 class="white" style="font-size: 200px; position: absolute; top: 110px; right: 160px;">Clock</h1>
<h1 style="font-size: 100px; position: absolute; top: 290px; right: 160px;">Synchronization</h1>

!SLIDE
.notes Because of the wider support for TOTP as well as ease of use and implementation, I choose TOTP as the basis for Rodauth's 2 factor authentication support.

<img src="../../file/rodauth.svg" width="400" style="margin-top: 150px;"/>
<h1 class="white" style="font-size: 130px; position: absolute; top: 390px; right: 80px;">TOTP</h1>

!SLIDE
.notes Rodauth's TOTP feature is separate from the login feature.  This makes it easy to support a wide variety of authentication needs. You can force all users that have accounts to use TOTP.  You can make TOTP use optional and only require users use it if they sign up for it.  You can also only require TOTP use for sensitive actions.

<h1 class="white" style="font-size: 100px; position: absolute; top: 120px; left: 140px;">Separate</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">TOTP</h1>
<h1 class="white" style="font-size: 70px; position: absolute; top: 310px; left: 230px;">from</h1>
<h1 style="font-size: 130px; position: absolute; top: 350px; left: 170px;">Login</h1>

!SLIDE
.notes One of the issues 2 factor authentication implementations have to deal with is how to handle the situation where the usual second factor is not available, for example with a lost mobile phone.

<h1 style="font-size: 130px; position: absolute; top: 240px; right: 360px;">Lost</h1>
<h1 class="white" style="font-size: 170px; position: absolute; top: 310px; right: 260px;">Phone?</h1>

!SLIDE
.notes Rodauth supports the use of recovery codes, which are single use passwords that can be used instead of TOTP authentication codes.

<h1 style="font-size: 130px; position: absolute; top: 410px; left: 260px;">Recovery</h1>
<h1 class="white" style="font-size: 130px; position: absolute; top: 510px; left: 340px;">Codes</h1>

!SLIDE
.notes Rodauth also supports sending authentication codes via SMS, if users have registered their mobile phone number.

<h1 class="white" style="font-size: 130px; position: absolute; top: 400px; left: 260px;">SMS</h1>
<h1 style="font-size: 130px; position: absolute; top: 510px; left: 340px;">Codes</h1>

!SLIDE
.notes Another issue 2 factor authentication implementations have to deal with is how to handle brute force attacks.  TOTP auth codes and SMS auth codes are usually 6 decimal digits, with means there are only 1 million combinations, which is fairly trivial to brute force.

<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Brute</h1>
<h1 style="font-size: 130px; position: absolute; top: 330px; left: 160px;">Force</h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 240px; left: 520px;">Attack</h1>

!SLIDE
.notes Rodauth defaults to locking out TOTP authentication and SMS authentication after 5 failed attempts.  After TOTP authentication and SMS authentication have been locked out, a recovery code has to be entered in order to gain access to the account.

<h1 class="white" style="font-size: 130px; position: absolute; top: 240px; left: 160px;">TOTP</h1>
<h1 style="font-size: 170px; position: absolute; top: 210px; left: 540px;">Lockout</h1>

!SLIDE skip
.notes Rodauth defaults to requiring a user to reenter their password whenever changing their TOTP security settings, as well as already being authenticated via TOTP before disabling 2 factor authentication or viewing recovery codes.

<h1 class="white" style="font-size: 130px; position: absolute; top: 240px; left: 160px;">TOTP</h1>
<h1 style="font-size: 170px; position: absolute; top: 210px; left: 540px;">Security</h1>

!SLIDE
.notes By using Rodauth's 2 factor authentication support, you can protect your site from password hash attacks on other sites, in addition to protecting other sites from password hash attacks on your site.

<h1 class="white" style="font-size: 130px; position: absolute; top: 110px; left: 70px;">Protect</h1>
<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Against</h1>
<h1 style="font-size: 100px; position: absolute; top: 320px; left: 310px;">Cracked</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 400px; left: 360px;">Passwords</h1>
<div class="review">Review</div>

!SLIDE
.notes I am now going to talk briefly about Rodauth's approach to token security.

<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; right: 160px;">Token</h1>
<h1 style="font-size: 100px; position: absolute; top: 520px; right: 140px;">Security</h1>

!SLIDE
.notes First, what types of tokens are we talking about?  Well, if a user forgets their password and needs to reset it, Rodauth generates a password reset token for the user and emails it to them.|The user receives the email, which contains a link with the token, allowing them to reset their password.  Similar tokens are used for other features in Rodauth.

<h1 class="white" style="font-size: 230px; position: absolute; top: 310px; right: 160px;">Tokens</h1>

!SLIDE
.notes One of the security advantages to Rodauth's tokens is that each token is account-specific.  In Rodauth, a password reset token for one user would never be valid for another user.  Other authentication libraries I reviewed appear to use purely random tokens.

<h1 style="font-size: 100px; position: absolute; top: 180px; right: 160px;">Account</h1>
<h1 style="font-size: 100px; position: absolute; top: 270px; right: 160px;">Specific</h1>
<h1 class="white" style="font-size: 230px; position: absolute; top: 310px; right: 160px;">Tokens</h1>

!SLIDE
.notes Rodauth tokens use the following format.

<pre style="font-size: 40px; margin-top: 210px;"><code>1234_hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU
</code></pre>

!SLIDE
.notes The first part of the token is the users account id, which is the part of the token that is account specific.

<pre style="font-size: 40px; margin-top: 210px;"><code><b>1234</b>_hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU
</code></pre>

!SLIDE
.notes The remainder of the token is randomly generated.

<pre style="font-size: 40px; margin-top: 210px;"><code>1234_<b>hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU</b>
</code></pre>

!SLIDE
.notes To authenticate the token, it is first split into two parts, the account id and the key.

<pre style="font-size: 40px; margin-top: 210px;"><code>{
  account_id: 1234,
  key: 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'
}
</code></pre>

!SLIDE
.notes Rodauth will look in the table storing the tokens, and find the row matching the account.

<pre style="font-size: 40px; margin-top: 210px;"><code>{
  account_id: <b>1234</b>,
  key: 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'
}

</code></pre>

!SLIDE
.notes Rodauth then checks if the key given matches the stored key.  If so, the token is valid, otherwise, it is not.

<pre style="font-size: 40px; margin-top: 210px;"><code>{
  account_id: 1234,
  key: <b>'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'</b>
}
</code></pre>

!SLIDE
.notes The query Rodauth uses when retrieving a token is specific to the related account, returning the stored key.

<pre style="font-size: 40px; margin-top: 210px;"><code>{
  account_id: 1234,
  key: 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'
}

SELECT <b>key</b> FROM account_password_reset_keys
WHERE <b>id = 1234</b>;
</code></pre>

!SLIDE
.notes Using account specific tokens decreases the probability that a brute force attack on a token will work.  The issue with purely random tokens is that you can brute force attack all tokens at the same time.

<h1 style="font-size: 130px; position: absolute; top: 210px; left: 160px;">Brute</h1>
<h1 style="font-size: 130px; position: absolute; top: 330px; left: 160px;">Force</h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 240px; left: 520px;">Attack</h1>

!SLIDE
.notes Other authentication frameworks will lookup tokens using a query like this.  The problem with this approach is that it increases the odds of a successful brute force attack into an arbitrary account by the number of rows that have a token.

<pre style="font-size: 40px; margin-top: 210px;"><code>SELECT <b>id</b> FROM accounts
WHERE <b>password_reset_key = </b>
      <b>'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'</b>;
</code></pre>

!SLIDE
.notes This is mostly a theoretical vulnerability as long as the number of possible tokens is much larger than the number of accounts that have tokens, which should be true.

<h1 class="white" style="font-size: 100px; position: absolute; top: 240px; left: 320px;">Mostly</h1>
<h1 style="font-size: 200px; position: absolute; top: 280px; left: 120px;">Theoretical</h1>

!SLIDE
.notes Another possible attack is a timing attack.

<h1 class="white" style="font-size: 150px; position: absolute; top: 310px; left: 360px;">Timing</h1>
<h1 style="font-size: 130px; position: absolute; top: 430px; left: 610px;">Attack</h1>

!SLIDE
.notes Because other authentication frameworks lookup by the key value, and have a database index on the key value, the time it takes to lookup up an account via a token depends on the number of characters at the start of the submitted token that match any existing token.

<pre style="font-size: 40px; margin-top: 210px;"><code>SELECT id FROM accounts
WHERE password_reset_key = 
      'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU';
</code></pre>

!SLIDE
.notes If a user submits this token, but there is an existing token in the system that also starts with hJ but not hJx, it will in general take less time

<pre style="font-size: 40px; margin-top: 210px;"><code>SELECT id FROM accounts
WHERE password_reset_key = 
      '<b>hJx</b>ZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU';
</code></pre>

!SLIDE
.notes than if there is an existing token that matches most of the start of the submitted token.

<pre style="font-size: 40px; margin-top: 210px;"><code>SELECT id FROM accounts
WHERE password_reset_key = 
      '<b>hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbx</b>HbKiOU';
</code></pre>

!SLIDE
.notes Exploiting timing attacks over the internet is fairly difficult, especially in this case as the difference in speed is very low and subject to subtantial variance. Because of those facts, this is also mostly a theoretical vulnerability.

<h1 style="font-size: 150px; position: absolute; top: 110px; left: 330px;">Also</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 240px; left: 320px;">Mostly</h1>
<h1 style="font-size: 200px; position: absolute; top: 280px; left: 120px;">Theoretical</h1>

!SLIDE
.notes Another difference between Rodauth and other authentication libraries is Rodauth uses separate tables to store each separate type of token, instead of storing all token data as separate columns in a single table.

<h1 style="font-size: 150px; position: absolute; top: 80px; left: 20px;">Separate</h1>
<h1 style="font-size: 100px; position: absolute; top: 220px; left: 115px;">Tables</h1>
<h1 class="white" style="font-size: 70px; position: absolute; top: 310px; left: 220px;">Per</h1>
<h1 style="font-size: 150px; position: absolute; top: 330px; left: 100px;">Feature</h1>

!SLIDE
.notes If we go back to the Rodauth query I showed earlier, note that the table name it selects from is specific to password resets.

<pre style="font-size: 40px; margin-top: 210px;"><code>SELECT key FROM <b>account_password_reset_keys</b>
WHERE id = 1234;
</code></pre>

!SLIDE
.notes This is not for security, it is for performance and storage space.  If you have 10 million accounts, and only 1 thousand have password reset tokens at any one time, it is better for performance and storage space to have only 1000 rows of tokens, compared to having mostly NULL values for 10 million rows.

<h1 style="font-size: 200px; position: absolute; top: 180px; left: 20px;">Performance</h1>

!SLIDE
.notes In review, Rodauth uses account specific tokens stored in separate tables, mostly for performance and storage space. It also tries to prevent against theoretical brute force and timing attacks on tokens.

<h1 class="white" style="font-size: 130px; position: absolute; top: 410px; right: 160px;">Token</h1>
<h1 style="font-size: 100px; position: absolute; top: 520px; right: 140px;">Security</h1>
<div class="review">Review</div>

!SLIDE
.notes Next I am going to briefly describe each of Rodauth's features.  Rodauth attempts to support most common authentication and account management features.

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 160px;">Features</h1>

!SLIDE
.notes Rodauth does not load any features by default.  You need to explicitly load each feature you want to use.  If you are not using a feature, you do not have to pay any memory or performance cost for that feature.

<h1 class="white" style="font-size: 150px; position: absolute; top: 100px; left: 180px;">Only Pay For</h1>
<h1 style="font-size: 150px; position: absolute; top: 220px; left: 220px;">What You Use</h1>

!SLIDE
!SLIDE
.notes The most commonly used features are login and logout.  Most of the discussion of password hash security related to how to authenticate users during login.

<h1 style="font-size: 150px; position: absolute; top: 180px; left: 220px;">Login</h1>
<h1 class="white" style="font-size: 70px; position: absolute; top: 330px; left: 380px;">and</h1>
<h1 style="font-size: 150px; position: absolute; top: 370px; left: 220px;">Logout</h1>

!SLIDE
.notes There is a feature that allows users to change their password,

<h1 style="font-size: 100px; position: absolute; top: 420px; left: 320px;">Change</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 480px; left: 220px;">Password</h1>

!SLIDE
.notes as well as one that allows users to change their login.

<h1 class="white" style="font-size: 100px; position: absolute; top: 420px; left: 320px;">Change</h1>
<h1 style="font-size: 150px; position: absolute; top: 500px; left: 220px;">Login</h1>

!SLIDE
.notes If you have users that forget their passwords, you can enable a feature to allow them to automatically reset their passwords.

<h1 style="font-size: 100px; position: absolute; top: 120px; left: 320px;">Reset</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 180px; left: 220px;">Password</h1>

!SLIDE
.notes You can enable a feature that allows users to create their own accounts,

<h1 style="font-size: 100px; position: absolute; top: 220px; left: 420px;">Create</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 280px; left: 320px;">Account</h1>

!SLIDE
.notes as well as a feature that requires users verify that they have access to the email address associated with the account.

<h1 class="white" style="font-size: 100px; position: absolute; top: 220px; left: 420px;">Verify</h1>
<h1 style="font-size: 150px; position: absolute; top: 280px; left: 320px;">Account</h1>

!SLIDE
.notes There is also a feature for users to close their own accounts.

<h1 style="font-size: 100px; position: absolute; top: 220px; left: 420px;">Close</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 280px; left: 320px;">Account</h1>

!SLIDE
.notes There is a feature called remember for automatically authenticating users based on a token stored in a cookie, keeping track of whether they logged in via password or cookie, and asking them to reauthenticate via password for security sensitive actions.

<h1 class="white" style="font-size: 150px; position: absolute; top: 220px; left: 420px;">Remember</h1>

!SLIDE
.notes To prevent bruteforce attacks, there is an account lockout feature that locks accounts out after a certain number of failed logins, as well as allowing users to unlock their account via email.

<h1 style="font-size: 150px; position: absolute; top: 220px; left: 420px;">Lockout</h1>

!SLIDE
.notes Rodauth by default requires users to reinput their password for account modifications, but it offers a feature so that it does not ask for a password if one has been entered recently.

<h1 class="white" style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Password</h1>
<h1 style="font-size: 150px; position: absolute; top: 280px; left: 210px;">Grace</h1>
<h1 class="white" style="font-size: 120px; position: absolute; top: 400px; left: 335px;">Period</h1>

!SLIDE
.notes If you have a policy that requires password complexity checks, Rodauth has a feature that offers a configurable password complexity checker.

<h1 style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Password</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 300px; left: 220px;">Complexity</h1>

!SLIDE
.notes If you have a policy that disallows the reuse of recent passwords, Rodauth supports that.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 240px;">Disallow</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Password</h1>
<h1 style="font-size: 150px; position: absolute; top: 270px; left: 240px;">Reuse</h1>

!SLIDE
.notes If you have a policy that requires that users change their passwords on a regular basis, Rodauth supports that too.

<h1 style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Password</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 300px; left: 220px;">Expiration</h1>

!SLIDE
.notes Rodauth also supports automatically expiring unused accounts,

<h1 class="white" style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Account</h1>
<h1 style="font-size: 150px; position: absolute; top: 300px; left: 220px;">Expiration</h1>

!SLIDE
.notes expiring sessions based on inactivity timeouts and max lifetimes,

<h1 style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Session</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 300px; left: 220px;">Expiration</h1>

!SLIDE
.notes as well as limiting each account to a single logged in session, automatically terminating any existing session when the user logs in.

<h1 class="white" style="font-size: 115px; position: absolute; top: 120px; left: 240px;">Single</h1>
<h1 style="font-size: 100px; position: absolute; top: 220px; left: 340px;">Session</h1>

!SLIDE
.notes Rodauth has an OTP feature implementing 2nd factor authentication via time-based one time passwords.

<h1 class="white" style="font-size: 200px; position: absolute; top: 420px; left: 320px;">OTP</h1>

!SLIDE
.notes Rodauth has an SMS codes feature implementing 2nd factor authentication via codes sent via SMS messages.

<h1 style="font-size: 200px; position: absolute; top: 150px; left: 270px;">SMS</h1>
<h1 class="white" style="font-size: 200px; position: absolute; top: 320px; left: 320px;">Codes</h1>

!SLIDE
.notes Rodauth has a recovery codes feature implementing 2nd factor authentication via single-use account recovery codes.

<h1 class="white" style="font-size: 150px; position: absolute; top: 200px; left: 130px;">Recovery</h1>
<h1 style="font-size: 200px; position: absolute; top: 320px; left: 320px;">Codes</h1>

!SLIDE
.notes Finally, Rodauth has a JWT feature, which adds JSON API support to all of Rodauth's features, storing authentication information in JWT tokens instead of sessions.

<h1 class="white" style="font-size: 200px; position: absolute; top: 300px; left: 330px;">JWT</h1>

!SLIDE
.notes In review, Rodauth is a full featured authentication and account management framework, which should be able to handle the needs of most applications.

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 160px;">Features</h1>
<div class="review">Review</div>

!SLIDE
.notes Rodauth is configured using a block based DSL that allows overriding all parts of the framework.  This configuration approach is what makes Rodauth flexible enough to support most existing authentication needs.

<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Configuration</h1>


!SLIDE
.notes Rodauth is implemented as a plugin to the Roda web toolkit. So this is the minimal usage of Rodauth in applications.|If you are using Roda, you can use Rodauth directly in your application as a plugin.  If you are not using Roda, I will discuss how to use Rodauth in the next section.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'
class App &lt; Roda
  plugin :rodauth do
    enable :login, :logout
  end

  route do |r|
    r.rodauth
  end
end
</code></pre>

!SLIDE
.notes All configuration for Rodauth happens inside the block you pass when loading the plugin.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'
class App &lt; Roda
  <b>plugin :rodauth do</b>
    <b>enable :login, :logout</b>
  <b>end</b>

  route do |r|
    r.rodauth
  end
end
</code></pre>

!SLIDE skip
.notes The route block here is executed for each request received by the application.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'
class App &lt; Roda
  plugin :rodauth do
    enable :login, :logout
  end

  <b>route do |r|</b>
    r.rodauth
  end
end
</code></pre>

!SLIDE skip
.notes In this example, all the route block does is call r.rodauth, which allows Rodauth to handle the request.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'
class App &lt; Roda
  plugin :rodauth do
    enable :login, :logout
  end

  route do |r|
    <b>r.rodauth</b>
  end
end
</code></pre>

!SLIDE
.notes Since this section is focused on configuration, let me focus just on the configuration block

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
end
</code></pre>

!SLIDE
.notes I mentioned earlier that rodauth does not load any features by default.  In Rodauth, all features you want to use must be specified explicitly.  You do this by calling enable with symbols for the features you want to use.  In this case, they are the login and logout features.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>plugin :rodauth do
  <b>enable :login, :logout</b>
end
</code></pre>

!SLIDE
.notes Each feature you load adds configuration methods related to the feature.  So after loading the login feature, you can call the after_login configuration method with a block to specify additional behavior to perform after a successful login.|In this case, we are recording successful logins to a log. 

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout

  after_login do
    LOGGER.info "#{account.email} logged in from #{request.ip}!"
  end
end
</code></pre>

!SLIDE
.notes The interesting things here are the calls to account and request.|You see, each of these configuration blocks is evaluated in the context of a Rodauth object.  This object has access to everything related to the request.|So you can change how Rodauth will handle requests using any information related the request.  This design is what makes Rodauth flexible enough to handle most authentication needs.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout

  after_login do
    LOGGER.info "#{<b>account</b>.email} logged in from #{<b>request</b>.ip}!"
  end
end
</code></pre>

!SLIDE
.notes For simplicity, Rodauth allows you to use arguments for many simple configuration settings.  The accounts_table method sets the database table used to store accounts.  You can specify this by just passing a symbol to the method.  This is a useful shortcut.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout

  <b>accounts_table :users</b>
end
</code></pre>

!SLIDE
.notes However, you can pass a block to accounts_table if you want to be able to change which accounts table per request.  All of Rodauth's configuration settings accept blocks to allow more detailed control during configuration.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout

  accounts_table do
    if <b>request.ip.start_with?('192.168.1')</b>
      <b>:admins</b>
    else
      <b>:users</b>
    end
  end
end
</code></pre>


!SLIDE
.notes I will describe briefly how this is accomplished internally, though I will simplify it a bit.  The context in which the blocks are evaluated is an instance of a class I will call Auth.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Auth
end
</code></pre>

!SLIDE
.notes Each of the features you load into Rodauth is a separate module.  The defaults for Rodauth are normal instance methods in these modules.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>module Login
  def after_login
  end
end
</code></pre>

!SLIDE
.notes When you enable the login and logout features in Rodauth, it is equivalent to including those modules into the Auth class.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Auth
  include Login
  include Logout
end
</code></pre>

!SLIDE
.notes And when you call one of the configuration methods, you are actually defining an instance method in the Auth class.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Auth
  include Login
  include Logout

  def accounts_table
    if request.ip.start_with?('192.168.1')
      :admins
    else
      :users 
    end
  end
end
</code></pre>

!SLIDE
.notes The configuration DSL is a shortcut to create an authentication class that includes correct modules and has certain methods overridden as needed.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout

  accounts_table do
    if request.ip.start_with?('192.168.1')
      :admins 
    else
      :users
    end
  end
end
</code></pre>

!SLIDE skip
.notes One consequence of this approach is that in all blocks given to configuration methods, you can call super to get Rodauth's default behavior.  Because you are defining methods via a block, you must pass explicit arguments to super, which in this case is no arguments.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout

  accounts_table do
    if request.ip.start_with?('192.168.1')
      :admins
    else
      <b>super()</b>
    end
  end
end
</code></pre>

!SLIDE
.notes Another interesting aspect of Rodauth's internals is that while I mentioned that features like Login and Logout and modules, that is only part of the story.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Auth
  include <b>Login</b>
  include Logout
end
</code></pre>

!SLIDE
.notes They are not modules in the sense that they are not defined with the module keyword.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code><b>module</b> Login
end
</code></pre>

!SLIDE
.notes Rodauth actually has a class called Feature, which all of the features are instances of.  

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Feature &lt; Module
end
</code></pre>

!SLIDE
.notes As you can see here, you can subclass Module in ruby in order to create custom module subclasses.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class <b>Feature &lt; Module</b>
end
</code></pre>

!SLIDE
.notes The reason for doing this is that you can then define instance methods in this module subclass.  Here we define a depends method to set dependencies for the feature.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Feature &lt; Module
  <b>def depends(*d)</b>
    dependencies.concat(deps)
  end
end
</code></pre>

!SLIDE
.notes Those instance methods are then callable as module methods when creating your feature.  Here we call the depends method, to set that the lockout feature depends on the login feature. This makes it so loading the lockout feature will automatically load the login feature first.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>Lockout = Feature.new do
  <b>depends :login</b>
end
</code></pre>

!SLIDE
.notes In addition to handling dependencies, Rodauth also needs to handle cases where two features interact without depending on each other.  One example of this is the close account feature.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>CloseAccount = Feature.new do
  def after_close_account
    DB[:account_password_hashes].
      where(:account_id=>account_id_value).delete
  end
end
</code></pre>

!SLIDE
.notes After closing an account, the close account feature removes the account's password hash.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>CloseAccount = Feature.new do
  def after_close_account
    <b>DB[:account_password_hashes].</b>
      <b>where(:account_id=>account_id_value).delete</b>
  end
end
</code></pre>

!SLIDE
.notes However, if you are using the reset password feature, you may also want any password reset keys to be deleted when an account is closed.|How do you handle the case where multiple features need to interact correctly when used together and also when used separately, and where the features can be added in either order?

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>CloseAccount = Feature.new do
  def after_close_account
    DB[:account_password_hashes].
      where(:account_id=>account_id_value).delete
  end
end


<b>ResetPassword</b> = Feature.new do
  def after_close_account
    DB[<b>:account_password_reset_keys</b>].
      where(:account_id=>account_id_value).delete
  end
end
</code></pre>

!SLIDE
.notes I have found that the best way to accomplish this is to use super if defined?(super).  This will call a superclass method if a superclass method exists, but do nothing if a superclass method does not exist.  By doing this, you can have multiple features work properly when used together and when used separately, without either depending on the other.

<pre class="sh_ruby" style="font-size: 32px; margin-top: 10px;"><code>CloseAccount = Feature.new do
  def after_close_account
    DB[:account_password_hashes].
      where(:account_id=>account_id_value).delete
    <b>super if defined?(super)</b>
  end
end

ResetPassword = Feature.new do
  def after_close_account
    <b>super if defined?(super)</b>
    DB[:account_password_reset_keys].
      where(:account_id=>account_id_value).delete
  end
end
</code></pre>

!SLIDE
.notes In review, Rodauth is configured via a simple but flexible DSL, which allows it to handle the authentication and account management needs for most applications.

<h1 class="white" style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Configuration</h1>
<div class="review">Review</div>


!SLIDE
.notes I would now like to show how you can easily integrate Rodauth into your applications.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 360px;">Integration</h1>


!SLIDE
.notes First, I want to go over how Rodauth can be used in web applications that do not use the Roda web toolkit.

<h1 style="font-size: 100px; position: absolute; top: 180px; left: 220px;">Other</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 270px; left: 270px;">Web</h1>
<h1 style="font-size: 100px; position: absolute; top: 400px; left: 320px;">Frameworks</h1>

!SLIDE
.notes Roda supports a middleware plugin, which allows the Roda application to be used as Rack middleware. So to use Rodauth in another web framework, you just create a small Roda app, load the middleware and rodauth plugins, and have the route block call r.rodauth.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'

class RodauthApp &lt; Roda
  <b>plugin :middleware</b>
  plugin :rodauth do
    enable :login, :logout
  end
  route do |r|
    r.rodauth
    r.redirect('/login') unless rodauth.logged_in?
    env['rodauth'] = rodauth
  end
end

use RodauthApp
</code></pre>

!SLIDE
.notes In this case, if Rodauth does not handle the request and the user is not logged in, the Roda middleware will redirect them to the login page, which Rodauth will handle. If the user is logged in, any route that Rodauth does not handle will be passed to the application.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'

class RodauthApp &lt; Roda
  plugin :middleware
  plugin :rodauth do
    enable :login, :logout
  end
  route do |r|
    r.rodauth
    <b>r.redirect('/login') unless rodauth.logged_in?</b>
    env['rodauth'] = rodauth
  end
end

use RodauthApp
</code></pre>

!SLIDE
.notes You can set the rodauth object in the request's environment, making it easy for the application to get access to it.|You may be concerned about the extra overhead using Rodauth via Roda middleware, but fear not, Roda is very light framework, and only adds about 2 megabytes to your application's memory overhead.  

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'

class RodauthApp &lt; Roda
  plugin :middleware
  plugin :rodauth do
    enable :login, :logout
  end
  route do |r|
    r.rodauth
    r.redirect('/login') unless rodauth.logged_in?
    <b>env['rodauth'] = rodauth</b>
  end
end

use RodauthApp
</code></pre>

!SLIDE skip
.notes For database access, Rodauth uses Sequel internally, but you can certainly use ActiveRecord in your application and still use Rodauth. You would have to setup a Sequel database connection, but that is pretty much it.|Sequel is not as small as Roda, but it still adds less than 8 megabytes to your application's memory overhead.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'sequel'
<b>DB = Sequel.connect('postgres://...')</b>

plugin :rodauth do
  enable :login, :logout
  <b>db DB</b>
  <b>accounts_table :users</b>
end
</code></pre>

!SLIDE
.notes What if you want to authenticate using LDAP, say to Windows Active Directory?  That can be accomplished using a single configuration method.  You just call the password_match? configuration method with a block.  Using the already retrieved account and the provided password, you can use any custom authentication method that you want.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  <b>password_match? do |password|</b>
    <b>LdapAuth.valid?(account.username, password)</b>
  end
end
</code></pre>

!SLIDE
.notes What if you want to have different authentication behavior for different sections of your application?  Maybe you want to separate admin account authentication from regular account authentication.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
end

plugin :rodauth, :name=>:admin do
  enable :login, :logout, :change_password
  accounts_table :admins
  password_hash_table :admin_password_hashes
end
</code></pre>

!SLIDE
.notes Rodauth supports this.  You can load the Rodauth plugin multiple times with different names to store different Rodauth configurations.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
end

plugin :rodauth, <b>:name=>:admin</b> do
  enable :login, :logout, :change_password
  accounts_table :admins
  password_hash_table :admin_password_hashes
end
</code></pre>

!SLIDE
.notes Then, in your routing tree block, you can call r.rodauth with no arguments to use the default configuration.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>route do |r|
  r.on "admin" do
    r.rodauth(:admin)
  end

  <b>r.rodauth</b>
end
</code></pre>

!SLIDE
.notes But in the admin section of your site, you can call r.rodauth with the symbol admin, which will use the admin configuration.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>route do |r|
  <b>r.on "admin"</b> do
    <b>r.rodauth(:admin)</b>
  end

  r.rodauth
end
</code></pre>

!SLIDE skip
.notes Let us take a brief look at a real world example of Rodauth usage.  This is from one of my open source applications called Giftsmas, which is a gift tracking program.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  accounts_table :users
  account_password_hash_column :password_hash
  title_instance_variable :@title
end
</code></pre>

!SLIDE skip
.notes As I showed earlier, the first step is to enable the the required features, in this case login and logout.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  <b>enable :login, :logout</b>
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  accounts_table :users
  account_password_hash_column :password_hash
  title_instance_variable :@title
end
</code></pre>

!SLIDE skip
.notes These methods all override the default values so that the user interface for Giftsmas did not change when its custom authentication implementation was converted to use Rodauth.|No changes were required to the Giftsmas integration tests when converting it to use Rodauth.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  <b>session_key :user_id</b>
  <b>login_param 'user'</b>
  <b>login_label 'User'</b>
  <b>login_column :name</b>
  <b>accounts_table :users</b>
  account_password_hash_column :password_hash
  <b>title_instance_variable :@title</b>
end
</code></pre>

!SLIDE skip
.notes Before I converted Giftsmas to use Rodauth, it already stored bcrypt password hashes in a column in the same table, so Giftsmas uses the account_password_hash_column configuration method.|I run the Giftsmas demo site on Heroku, so I cannot easily switch it to use Rodauth's more secure default approach.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  accounts_table :users
  <b>account_password_hash_column :password_hash</b>
  title_instance_variable :@title
end
</code></pre>

!SLIDE
.notes Hopefully these examples show how Rodauth can be integrated into existing applications, due to how flexible its configuration is.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 360px;">Integration</h1>
<div class="review">Review</div>

!SLIDE
.notes Security, Simplicitity, and Flexibility.  These are Rodauth's three design goals.

<div class="review" style="margin-top: 40px;">Design Goals</div>
<h1 style="font-size: 150px; position: absolute; top: 140px; left: 220px;">Security</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 270px; left: 270px;">Simplicity</h1>
<h1 style="font-size: 150px; position: absolute; top: 400px; left: 340px;">Flexibility</h1>

!SLIDE 
.notes If you value these goals and want to learn more about Rodauth, check out rodauth.jeremyevans.net for details.

<img src="../../file/rodauth.svg" width="400" style="position: absolute; top: 50px; left: 400px;"/>
<h1 style="text-align: left; margin-left: 100px; margin-top: 580px; font-size: 70px;">http://rodauth.jeremyevans.net</h1>

!SLIDE
.notes I would like to end this presentation by discussing some ideas I would like you to take with you, not specifically related to Rodauth.

<h1 style="font-size: 200px; position: absolute; top: 180px; left: 220px;">Takeaways</h1>


!SLIDE
.notes First, prefer longer passwords over shorter but more complex passwords.  As I showed earlier, longer passwords can be significantly more secure than shorter but more complex passwords, and are often easier to remember.

<h1 style="font-size: 100px; position: absolute; top: 180px; left: 220px;">Prefer</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 240px; left: 120px;">Longer</h1>
<h1 style="font-size: 100px; position: absolute; top: 380px; left: 220px;">Passwords</h1>

!SLIDE
.notes Second, if you store password hashes, always use bcrypt or scrypt as the password hash algorithm.  For important passwords, such as administrative passwords, consider using a higher than default cost factor.

<h1 class="white" style="font-size: 200px; position: absolute; top: 340px; left: 120px;">Use</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 460px;">bcrypt</h1>
<h1 style="font-size: 130px; position: absolute; top: 430px; left: 460px;">scrypt</h1>


!SLIDE
.notes Third, I would like you to consider using multiple database accounts in your applications where it makes sense from a security perspective.  I rarely see applications that do this.  While it does take some additional work to setup and maintain, if security is important, the benefits of doing so should outweigh the costs.

<h1 style="font-size: 150px; position: absolute; top: 330px; left: 140px;">Multiple</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 310px; left: 730px;">Database</h1>
<h1 class="white" style="font-size: 100px; position: absolute; top: 410px; left: 730px;">Accounts</h1>


!SLIDE
.notes Fourth, recognize that database tables are cheap.  In reviewing other authentication frameworks and other applications, I see a tendency to use tables with many NULLable columns, which is considered a smell from a database design perspective.  Database tables are not a limited resource, do not be afraid to use more of them.

<h1 style="font-size: 100px; position: absolute; top: 180px; left: 120px;">Database</h1>
<h1 style="font-size: 100px; position: absolute; top: 270px; left: 120px;">Tables</h1>
<h1 class="white" style="font-size: 150px; position: absolute; top: 260px; left: 420px;">Are Cheap</h1>


!SLIDE
.notes Finally, before creating your own library, review existing libraries that do the same or similar things.  Before I started work on Rodauth, I reviewed Devise, Authlogic, and Sorcery.|Originally, my goal was to see if any of them would be flexible enough to handle the needs of all of my applications, and if it would be possible to use them without Rails.|Ultimately, I ended up writing Rodauth, but the knowledge I gained from reviewing existing libraries was very helpful to me in doing so.

<h1 class="white" style="font-size: 100px; position: absolute; top: 180px; left: 220px;">Read</h1>
<h1 style="font-size: 150px; position: absolute; top: 260px; left: 130px;">Existing</h1>
<h1 style="font-size: 100px; position: absolute; top: 400px; left: 290px;">Libraries</h1>


!SLIDE 
.notes That concludes my presentation.  I would like to thank the organizers for inviting me to speak to all of you today, as well as thank all of you for listening to me.

<h1 class="white" style="font-size: 100px; position: absolute; top: 380px; left: 420px;">Thank</h1>
<h1 style="font-size: 200px; position: absolute; top: 400px; left: 300px;">You</h1>

!SLIDE 
.notes If any of you have questions, I will be happy to answer them now.

<h1 style="text-align: left; margin-left: 50px; margin-top: 280px; font-size: 170px; color: white;">Questions?</h1>
