!SLIDE bg-roda
.notes Hello everyone.  I'm very excited and honored to be speaking here at RubyConf LT.|My presentation today is entitled Rodauth: Website Security Through Database Security, and it will discuss the design and implementation of the Rodauth authentication framework.

<h1 class="roda" style="font-size: 150px; position: absolute; top: 150px; left: 300px;">Rodauth</h1>
<h1 class="roda" style="font-size: 70px; position: absolute; top: 450px; left: 300px;">Website Security</h1>
<h1 class="roda" style="font-size: 50px; position: absolute; top: 550px; left: 300px;">Through</h1>
<h1 class="roda" style="font-size: 70px; position: absolute; top: 620px; left: 300px;">Database Security</h1>
<h1 class="roda" style="font-size: 70px; position: absolute; bottom: 10px; left: 40px; text-align: left; line-height: 75%;">RubyConfLT 2016</h1>

!SLIDE
.notes My name is Jeremy Evans, and I've been using Ruby since 2004. I'm the maintainer of numerous ruby libraries,

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">GitHub: jeremyevans</h1>
<h1 style="font-size: 130px; position: absolute; top: 510px; left: 360px;">Twitter: @jeremyevans0</h1>


!SLIDE
.notes the most popular of which is Sequel, the database toolkit for Ruby.

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Sequel</h1>


!SLIDE
.notes For the last couple years, I've also been working on Roda, which is a full featured web framework toolkit for building maintainable and fast web applications in ruby.

<h1 style="font-size: 230px; position: absolute; top: 210px; left: 160px;">Roda</h1>


!SLIDE
.notes As most or all of you know, Ruby's most popular web framework is Rails. One of the disadvantages to using a non-Rails web framework, is that so many libraries are built to work just with Rails.

<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Rails</h1>


!SLIDE
.notes For example, if you are looking for a full featured authentication framework, in you are using Rails, there's Devise, Authlogic, and Sorcery.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Devise</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 360px;">Authlogic</h1>
<h1 style="font-size: 130px; position: absolute; top: 510px; left: 560px;">Sorcery</h1>

!SLIDE
.notes Because I worked mostly on non-Rails applications, I ended up reimplementing authentication differently in each of the applications I worked on.|After converting all of my applications to Roda, I decided last year to work on a authentication framework that all of my applications could use, which I named Rodauth.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Rodauth</h1>

!SLIDE
.notes One of the main design principles as I was developing Rodauth was that it had to be flexible.  I had many applications with different authentication requirements.  I had applications that where password hashes were stored in the same table as the logins, applications that authenticated against LDAP, applications that needing separate authentication for separate groups of users, and applications that used database functions for authentication.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Flexible</h1>

!SLIDE
.notes Another design principle was that I wanted the the default settings to be as secure as possible, and specifically make it exceptionally difficult for an attacker to get access to password hashes.  I'll talk a bit later about why this is so important.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Secure</h1>

!SLIDE
.notes The final design principle is that I wanted Rodauth to be simple to use and configure.  One of the most common security issues in applications is misconfiguration, and making configuration as simple as possible I think leads to more secure applications.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Simple</h1>

!SLIDE
.notes I also wanted Rodauth to be full featured.  I wanted to be able to start a new application and with a few lines of code enable not just login authentication, but account creation and verification, password changing and resets, and bruteforce protection. Other than the Rails-dependent authentication frameworks, most other ruby authentication frameworks only offer login authentication, forcing you to implement the other features yourself.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Full Featured</h1>

!SLIDE
.notes So that's the history behind and design principles of Rodauth.  With that out of the way, let me go over the sections in the rest of the presentation.  I'm first going to talk about password hash storage, why it is important, and how Rodauth handles.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Password Hash Storage</h1>

!SLIDE
.notes I'll then talk about about how Rodauth handles tokens used for things like password resets and account verification.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Token Storage</h1>

!SLIDE
.notes I'll talk briefly about the features Rodauth offers,

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Features</h1>

!SLIDE
.notes then talk about Rodauth's configuration DSL and the flexibility it offers to handle any authentication needs.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Configuration</h1>

!SLIDE
.notes Finally, I'll talk about how Rodauth can be integrated into existing systems.  It doesn't matter if you are using Rails, Sinatra, Padrino, are running on bare rack, or using one of the less well know Ruby web frameworks, you can use Rodauth to handle authentication into your application.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Integration</h1>

!SLIDE
.notes OK, now for the main part of the presentation.  I'm going to talk about what makes Rodauth different from authentication libraries from a security perspective, which is the use of multiple database accounts and database functions for authentication by default, which make it much more difficult for attackers to get access to the underlying password hashes.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Password Hash Storage</h1>

!SLIDE
.notes First, why is password hash storage so important.  Well, the main reason is that humans are bad at remembering good passwords, which causes two problems.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Why?</h1>

!SLIDE
.notes First, humans tend to choose simple passwords.  Even if you try to force them to use complex passwords, most humans will pick a simple password that just happens to more complex than you require.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Simple Passwords</h1>

!SLIDE
.notes For example, if you don't have any requirements on passwords, most users will use passwords like 123456 or password

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">123456</h1>
<h1 style="font-size: 130px; position: absolute; top: 310px; left: 160px;">password</h1>

!SLIDE
.notes If you require that passwords must contain both letters and numbers and must be at least eight characters, most users will use passwords like password1.  This is not significantly more secure than just password by itself.

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 160px;">password1</h1>

!SLIDE
.notes Password complexity requirements only make most users more likely to write down the password. If a user will not use a secure password in the absense of password complexity requirements, it is highly unlikely they will use a secure password if you have password complexity requirements.

<h1 style="font-size: 130px; position: absolute; top: 510px; left: 160px;">Password Complexity Requirements</h1>

!SLIDE
.notes Second, humans tend to reuse passwords on multiple sites.  It's pretty much impossible to prevent this.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Password Reuse</h1>

!SLIDE
.notes Because of these two factors, even if you are not storing any important data in your own system, if you are storing password hashes for users, and your application gets compromised, attackers can try to crack the password hashes stored in your system to try to figure out the underlying password, and use that password to access other systems.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Pivot</h1>

!SLIDE
.notes In order for an attacker to use password hashes from one website to attack another website, the first thing the attacker needs to do is get access to the password hashes.  Without access to the password hashes, there is nothing the attacker can use to attack other sites.  So as much as possible, you should make it very difficult for an attacker to access the password hashes.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Hash Access</h1>

!SLIDE
.notes Before I discuss how Rodauth prevents access to the password hashes, let's first discuss the worst case scenario, where an attacker is able to access all of the password hashes, The first thing they will probably do is to attempt to crack the passwords.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Password Cracking</h1>

!SLIDE
.notes This is generally done first using a dictionary attack.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Dictionary Attack</h1>

!SLIDE
.notes A dictionary attack basically tries each word in a dictionary, in order.  In English, there are only a few thousand words in a dictionary, so any password that is also a word that appears in a dictionary will be cracked almost immediately.  There many variations on dictionary attacks.

<pre style="font-size: 40px; margin-top: 10px;"><code>aardvark
aardwolf
aback
...
</code></pre>

!SLIDE
.notes One variation on a dictionary attack uses each dictionary word as a prefix, and checks different suffixes.  Passwords that use simple passwords like this will also be cracked quickly.

<pre style="font-size: 40px; margin-top: 10px;"><code>aardvark1 
aardvark2
...
aardvark9
aardwolf1
aardwolf2
...
...
</code></pre>

!SLIDE
.notes Another uses common substitutions of symbols and numbers for letters.  Passwords that use substitutions like this also tend of be cracked quickly.

<pre style="font-size: 40px; margin-top: 10px;"><code>@ardvark
a@rdvark
aardv@rk
...
@ardwolf
a@rdwolf
aardw0lf
aardwo1f
...
</code></pre>

!SLIDE
.notes The next attack is usually a brute force attack.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Brute Force Attack</h1>

!SLIDE
.notes A brute force attack basically tries all possible combinations of characters, and will eventually be able to crack any password given enough time.

<pre style="font-size: 40px; margin-top: 10px;"><code>a
b
..
z
aa
ab
..
zy
zz
..
</code></pre>

!SLIDE
.notes The amount of time it will take on average to crack a password via brute force depends on the length of the password and the charcter set it uses, as that decides the universe of possible passwords.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Combinations</h1>

!SLIDE
.notes For example, there are fewer than a half million 4 character all lowercase passwords but more than there are 208 billion possible 8 character all lowercase passwords, and more than 3 quadrillion 11 character all lowercase passwords.

<pre style="font-size: 40px; margin-top: 10px;"><code>
\[a-z]{4}       | 456976
\[a-z]{8}       | 2 * 10<sup>11</sup>
\[a-z0-9]{8}    | 3 * 10<sup>12</sup>
\[A-Za-z]{8}    | 5 * 10<sup>13</sup>
\[A-Za-z0-9]{8} | 2 * 10<sup>14</sup>
\[a-z]{11}      | 3 * 10<sup>15</sup>
</code></pre>

!SLIDE
.notes One thing to note here is that less complex but longer passwords are in general more secure than more complex but shorter passwords.  An 11 character all lower case password is about 16 times as secure as an 8 character password with uppercase and lowercase and numbers.

<pre style="font-size: 40px; margin-top: 10px;"><code>
\[a-z]{4}       | 456976
\[a-z]{8}       | 2 * 10<sup>11</sup>
\[a-z0-9]{8}    | 3 * 10<sup>12</sup>
\[A-Za-z]{8}    | 5 * 10<sup>13</sup>
<b>\[A-Za-z0-9]{8} | 2 * 10<sup>14</sup></b>
<b>\[a-z]{11}      | 3 * 10<sup>15</sup> 16x</b> 
</code></pre>

!SLIDE
.notes While you can use that information to choose more complex passwords for your own accounts. In general, you can't really force how complex users make their passwords.  However, you can choose which password hash algorithm to use, and that can have a dramatic effect on password hash cracking times.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Password Hash Algorithms</h1>

!SLIDE
.notes I'm sure many of you are familar with the SHA1 and SHA256 cryptographic hashes.  While these are good cryptographic hashes, they are very bad password hashes.  First, these are designed for speed, and you generally want password hashing to be slow, not fast, to make attacking the passwords more difficult.  Second, these hashes do not include salts, which mean that when using them for password hashing, the same password always results in the sane hash.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">SHA1/SHA256</h1>

!SLIDE
.notes For example, if you take the SHA1 hash of the word password, it always results in the same hash.

<pre style="font-size: 40px; margin-top: 10px;"><code>
$ echo -n password | sha1
5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8

$ echo -n password | sha1
5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8
</code></pre>

!SLIDE
.notes That's bad from a security perspective, as it makes the passwords possible to attack using a precomputed rainbow table, allowing all simple passwords to be cracked almost immediately.  A rainbow table of all 11 character all lower case SHA1 password hashes takes about 175 petabytes of storage, which is definitely within the range of many determined attackers, and would allow immediate cracking of any of these passwords.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Rainbow Table Attack</h1>

!SLIDE
.notes To prevent a rainbow table attack, you do what is called salting, where you add random data before the password, and when you check a password, you prepend the salt to the password and check the resulting hash matches.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Salting</h1>

!SLIDE
.notes In addition to salting, the other way to increase password security is to require more work to check that a hash matches.  Good password hash algorithms use many iterations of an underlying algorithm internally.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Many Iterations</h1>

!SLIDE
.notes These days, there are two good options for password hashes.  One is bcrypt, another is scrypt.  bcrypt is older and has been studied more, scrypt is newer and takes more memory in addition to a lot of CPU time, making it more resistant to some attacks using custom hardware.  Rodauth currently uses bcrypt.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">bcrypt/scrypt</h1>

!SLIDE
.notes For example, if you take the bcrypt hash of the word password, it results in different output, due to the inclusion of a random salt:

<pre style="font-size: 40px; margin-top: 10px;"><code>$ echo -n password | encrypt
$2b$08$tO1zyO2F8wRwISMvDg.YCuLUPoMDGwVPpl76vf5bXng3E4bRRCoui

$ echo -n password | encrypt
$2b$08$WdUcdTDMVgTNUFeQb/kWku7hfAf9R0JcHzwSb90NQPIpbF7tqiojO
</code></pre>

!SLIDE
.notes The highlighted part here is the salt for the password, with the remaining part being the hash itself. So to check a bcrypt password for validity, it first prepends the salt to the provided password, then it runs the bcrypt algorithm over the result, and then checks if the resulting hash matches.

<pre style="font-size: 40px; margin-top: 10px;"><code>$ echo -n password | encrypt
<b>$2b$08$tO1zyO2F8wRwISMvDg.YCu</b>LUPoMDGwVPpl76vf5bXng3E4bRRCoui

$ echo -n password | encrypt
<b>$2b$08$WdUcdTDMVgTNUFeQb/kWku</b>7hfAf9R0JcHzwSb90NQPIpbF7tqiojO
</code></pre>

!SLIDE
.notes Bcrypt also includes a cost factor as part of the salt, which each increase in the salt doubling the amount of work bcrypt will have to do to produce a hash.  In this case, the cost factor is 8.

<pre style="font-size: 40px; margin-top: 10px;"><code>$ echo -n password | encrypt
$2b$<b>08</b>$tO1zyO2F8wRwISMvDg.YCuLUPoMDGwVPpl76vf5bXng3E4bRRCoui
</code></pre>

!SLIDE
.notes On one of my test machines, which is a few years old, I can check about 7 million SHA1 password hashes per second.  I can check about 80 bcrypt password hashes with cost factor 8 per second.  So bcrypt with cost factor 8 is almost 100,000 times harder to crack than SHA1.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Time to Crack</h1>

!SLIDE
.notes So if we consider the time to crack each password by the number of possible passwords, we can compare about how long it would take to a password in each of these character sets depending on whether the hashes used SHA1 or bcrypt with cost factor 8.

<pre style="font-size: 40px; margin-top: 10px;"><code>set      | combinations |  SHA1   | bcrypt
\[a-z]{4}       | 456976    | &lt;1 second  | 1.5 hours
\[a-z]{8}       | 2 * 10<sup>11</sup> |  8 hours | 82 years
\[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;2 days  | 1 millenium
\[A-Za-z]{8}    | 5 * 10<sup>13</sup> | 88 days  | 21 millenia
\[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | &lt;1 year  | 86 millenia
\[a-z]{11}      | 3 * 10<sup>15</sup> | 16 years | 1454 millenia
</code></pre>

!SLIDE
.notes One thing we can see is that it takes more more time to crack an 8 character password hashed with bcrypt with cost factor 8 than it takes to crack an 11 character password hashed with SHA1.|Note that these numbers were for a single, relatively underpowered machine. Anyone serious about cracking passwords could easily spin up tens of thousands of more powerful cloud machines dedicated to cracking the password.|Let's see what the numbers look like for an serious attacker.

<pre style="font-size: 40px; margin-top: 10px;"><code>set      | combinations |  SHA1   | bcrypt
\[a-z]{4}       | 456976    |  &lt;1 second  | 1.5 hours
\[a-z]{8}       | 2 * 10<sup>11</sup> |  8 hours | <b>82 years</b>
\[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;2 days  | 1 millenium
\[A-Za-z]{8}    | 5 * 10<sup>13</sup> | 88 days  | 21 millenia
\[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | &lt;1 year  | 86 millenia
\[a-z]{11}      | 3 * 10<sup>15</sup> | <b>16 years</b> | 1454 millenia
</code></pre>

!SLIDE
.notes As you can see, against a serious attacker, all simple SHA1 password hashes can be cracked almost immediately, and most simple bcrypt passwords could be cracked if the attacker is willing to wait.

<pre style="font-size: 40px; margin-top: 10px;"><code>set      | combinations |  SHA1   | bcrypt
\[a-z]{8}       | 2 * 10<sup>11</sup> | &lt;1 second  | 43 minutes
\[a-z0-9]{8}    | 3 * 10<sup>12</sup> | &lt;1 second  | 9 hours
\[A-Za-z]{8}    | 5 * 10<sup>13</sup> |  7 seconds | 8 days
\[A-Za-z0-9]{8} | 2 * 10<sup>14</sup> | 30 seconds | 31 days
\[a-z]{11}      | 3 * 10<sup>15</sup> |  9 minutes | 1.5 years
</code></pre>

!SLIDE
.notes So now we can answer the question of why password hash storage is so important.  It's because if serious attackers get access to the password hashes, it probably won't take them that much time to crack them and get the passwords, and then they can use the passwords to attack other sites.  So preventing attackers from getting access to passwords is critical from a security perspective.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Why?</h1>

!SLIDE
.notes How do you keep the attacker from accessing the password hash, if you obviously need to get some access to the password hash in order to authenicate the user?

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">How?</h1>

!SLIDE
.notes Rodauth's approach is store password hashes in a separate table.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Separate Tables</h1>

!SLIDE
.notes Rodauth by default uses the accounts table to store accounts, and the account_password_hashes table to store password hashes.

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
.notes The account_password_hashes table has a foreign key reference to the accounts table that is also a primary key, ensuring there is only a single password hash for each account.

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

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">2 Database Accounts</h1>

!SLIDE
.notes One database account is used by the application itself, and has access to most of the tables, including the accounts table. For purposes of this presentation, I will call this account app.|A separate database account owns the password hash table.  I will refer to this account as ph.

<pre style="font-size: 40px; margin-top: 10px;"><code><b>-- Owned by application database account (app)</b>
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

<pre style="font-size: 40px; margin-top: 10px;"><code>REVOKE ALL ON account_password_hashes FROM public;
</code></pre>

!SLIDE
.notes Then the ph account grants access to insert, update, and delete password hashes to the app account.  This allows the app account to easily create new users, remove users, and change passwords for existing users. However, it doesn't allow the app account access to read the table.

<pre style="font-size: 40px; margin-top: 10px;"><code>REVOKE ALL ON account_password_hashes FROM public;
<b>GRANT INSERT, UPDATE, DELETE ON account_password_hashes TO app;</b>
</code></pre>

!SLIDE
.notes In order to authenticate without allowing access to the underlying hashes, Rodauth uses two database functions.  Both of the database functions are created by the ph account.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">2 Database Functions</h1>


!SLIDE
.notes The first function is called rodauth_get_salt, which returns the password salt.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_get_salt(account_id integer) RETURNS text AS $$
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

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_get_salt(<b>account_id integer</b>) RETURNS text AS $$
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

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_get_salt(account_id integer) RETURNS text AS $$
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
.notes Remember that the account_password_hashes stores the bcrypt password hash in a single column.  So in order to get the salt, it needs to extract the salt portion of the bcrypt hash.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_get_salt(account_id integer) RETURNS text AS $$
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
.notes The other important part is the use of SECURITY DEFINER when creating the function.  Setting SECURITY DEFINER for a database function is similar to setting an executable as setuid in Unix, in that the function executes using the permissions of the user that defined the function, instead of the user that is executing the function.|This is what allows the app account to check password hashes.  While the app account cannot access the table directly, it can call this function, which does have access to the table.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_get_salt(account_id integer) RETURNS text AS $$
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
.notes After the application retrieves the salt for the account, it uses the password provided by the user to construct the hash.  Then it needs to call a function to check if that hash is valid.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Application Computes Hash</h1>

!SLIDE
.notes That function is rodauth_valid_password_hash.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_valid_password_hash(account_id integer, hash text) RETURNS boolean AS $$
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

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_valid_password_hash(<b>account_id integer, hash text</b>) RETURNS boolean AS $$
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
.notes Like rodauth_get_salt, it finds the row with the matching id.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_valid_password_hash(account_id integer, hash text) RETURNS boolean AS $$
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
.notes And it just returns whether the password hash computed by the application matches the stored password hash.  If so, the user provided the correct password and should be allowed to login.  Otherwise, the user provided an incorrect password and should not be allowed to login.

<pre style="font-size: 40px; margin-top: 10px;"><code>CREATE OR REPLACE FUNCTION rodauth_valid_password_hash(account_id integer, hash text) RETURNS boolean AS $$
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
.notes By using this approach of disallowing direct access to password hashes, offers protections against two types of attacks.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Protections</h1>

!SLIDE
.notes The most common way that attackers get access to password hashes is by exploiting SQL injection vulnerabilities.  With Rodauth's approach, an attacker cannot get access to the password hash table.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">SQL Injection</h1>

!SLIDE
.notes For example, let's see what happens if an attacker tries to read all password hashes directly.  This simple SELECT query

<pre style="font-size: 40px; margin-top: 10px;"><code>SELECT * FROM account_password_hashes;
</code></pre>


!SLIDE
.notes results in an error, since the application's database account does not have access to this table.

<pre style="font-size: 40px; margin-top: 10px;"><code>SELECT * FROM account_password_hashes;
<b>ERROR: permission denied for relation account_password_hashes</b>
</code></pre>


!SLIDE
.notes More sophisticated hackers might try to grant themselves access to the table first.

<pre style="font-size: 40px; margin-top: 10px;"><code>GRANT SELECT ON account_password_hashes to my_account;
</code></pre>

!SLIDE
.notes However, while this doesn't result in an error, it also doesn't make any changes.  The reason for this is that it the application's database account is not the owner of the password hash table, and only the owner or a superuser can grant priveleges.

<pre style="font-size: 40px; margin-top: 10px;"><code>GRANT SELECT ON account_password_hashes to my_account;
<b>WARNING:  no privileges were granted for "account_password_hashes"</b>
</code></pre>


!SLIDE
.notes Maybe an attacker will try to change the owner on the table?

<pre style="font-size: 40px; margin-top: 10px;"><code>ALTER TABLE account_password_hashes OWNER TO my_account;
</code></pre>

!SLIDE
.notes This also raises as error, as you can't change the owner of a table you aren't currently the owner of.

<pre style="font-size: 40px; margin-top: 10px;"><code>ALTER TABLE account_password_hashes OWNER TO my_account;
<b>ERROR:  must be owner of relation account_password_hashes</b>
</code></pre>

!SLIDE
.notes Basically, Rodauth's approach is secure against SQL Injection.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Secure Against SQL Injection</h1>

!SLIDE
.notes Let us also consider a remote code execution vulnerability.  This is pretty much the worst type of vulnerability in an application, as the attacker can run arbitrary code.|The good news is that Rodauth is secure against this type of attack on existing password hashes.  Unless the attacker can get access to either the ph account or a database superuser account, they can't get access to the password hashes.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Remote Code Execution?</h1>

!SLIDE
.notes However, if an attack can remotely execute code, while they may not be able to get access to stored password hashes, they can fairly easily compromise any account that logs in while they have control of the system.  They don't need the password hash in this case, as a user logging in provides the plain text of their password.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Access During Login</h1>

!SLIDE
.notes There are some limitations with Rodauth's approach of using multiple database accounts and database functions for authentication.  First, it requires that you have the ability to use multiple accounts in your database.  This is fairly easy if you control the database.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Limitations</h1>

!SLIDE
.notes However, if you are using a service like Heroku to supply the database, you may not be able to use multiple accounts.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">Heroku</h1>

!SLIDE
.notes Additionally, Rodauth's use of multiple database accounts and functions is currently specific to PostgreSQL.  I believe the approach should be possible to use on any database that supports multiple accounts and functions that execute with the permissions of the definer instead of the permissions of the executer, but currently Rodauth only supports PostgreSQL by default.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">PostgreSQL Specific</h1>

!SLIDE
.notes When using a database other than PostgreSQL, Rodauth supports a authentication option called account_password_hash_column, which makes Rodauth handle security much like other Ruby authentication frameworks, storing the password hash as a column in the same table as the account, and just retrieving the hash and see if the user's password matches the hash. This does lose Rodauth's special security advantage, dropping the security level to roughly the same level as other authentication frameworks.

<h1 style="font-size: 130px; position: absolute; top: 110px; left: 160px;">account_password_hash_column</h1>


!SLIDE
.notes In review, Rodauth uses multiple database accounts and database functions to prevent attackers access to password hashes, and a secure password hash algorithm to slow attackers down if they do get access to the password hashes.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Password Hash Storage</h1>
<div class="review">Review</div>


!SLIDE
.notes I'm now going to talk briefly about token storage and how it relates to Rodauth's security.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Token Storage</h1>

!SLIDE
.notes First, what types of token's are we talking about?  Well, let's say a user forgets their password and needs to reset it.  Rodauth generates a password reset token for the user and mails it to them.  The user receives the mail, which contains a link that contains the token, allowing them to change their password.  Similar tokens are used for other features in Rodauth.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Tokens</h1>

!SLIDE
.notes One of the security advantages to Rodauth's tokens is that each token is account-specific.  In Rodauth, a password reset token for one user would never be valid for another user.  Other authentication libraries I reviewed appear to use purely random tokens.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Account-Specific Tokens</h1>

!SLIDE
.notes For example, tokens in Rodauth are in this format.

<pre style="font-size: 40px; margin-top: 10px;"><code>1234_hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU
</code></pre>

!SLIDE
.notes The first part of the token is the users account id, which is the part of the token that is account specific.

<pre style="font-size: 40px; margin-top: 10px;"><code><b>1234</b>_hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU
</code></pre>

!SLIDE
.notes The remainder of the token is randomly generated.

<pre style="font-size: 40px; margin-top: 10px;"><code>1234_<b>hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU</b>
</code></pre>

!SLIDE
.notes To authenticate the token, it is first split into two parts, the account id and the key.  It 

<pre style="font-size: 40px; margin-top: 10px;"><code>{
  account_id: 1234,
  key: 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'
}
</code></pre>

!SLIDE
.notes Rodauth will look at the table storing the token, and find the row matching the account.

<pre style="font-size: 40px; margin-top: 10px;"><code>{
  account_id: <b>1234</b>,
  key: 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'
}

</code></pre>

!SLIDE
.notes Rodauth then checks if the key given matches the stored key.  If so, the token is valid, otherwise, it is not.

<pre style="font-size: 40px; margin-top: 10px;"><code>{
  account_id: 1234,
  key: <b>'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'</b>
}
</code></pre>

!SLIDE
.notes The query Rodauth uses when retrieving a token is specific to the related account, returning the stored key.

<pre style="font-size: 40px; margin-top: 10px;"><code>{
  account_id: 1234,
  key: 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'
}

SELECT <b>key</b> FROM account_password_reset_keys
WHERE <b>id = 1234</b>;
</code></pre>

!SLIDE
.notes Using account specific tokens decreases the probability that a brute force attack on a token will work.  The issue with purely random tokens is that you can brute force attack all tokens at the same time.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Brute Force Attack</h1>

!SLIDE
.notes Other authentication frameworks will lookup tokens using a query like this.  The problem with this approach is that it increases the odds of a successful brute force attack into an arbitrary account by the number of rows that have a token.

<pre style="font-size: 40px; margin-top: 10px;"><code>SELECT <b>id</b> FROM accounts
WHERE <b>password_reset_key = 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU'</b>;
</code></pre>

!SLIDE
.notes This is mostly a theoretical vulnerability however, as long as the number of possible tokens is much larger than the number of accounts that have tokens, which should be true.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Mostly Theoretical</h1>

!SLIDE
.notes Another attack that is more possible is a timing attack.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Timing Attack</h1>

!SLIDE
.notes Because other authentication frameworks lookup by the key value, and have a database index on the key value, the time it takes to retrieve a token depends on the number of characters at the start of the submitted token that match any existing token.

<pre style="font-size: 40px; margin-top: 10px;"><code>SELECT id FROM accounts
WHERE password_reset_key = 'hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU';
</code></pre>

!SLIDE
.notes For example, if they submit this token, but there is an existing token in the system that also starts with hJ but not hJx, it will in general take less time

<pre style="font-size: 40px; margin-top: 10px;"><code>SELECT id FROM accounts
WHERE password_reset_key = '<b>hJx</b>ZkHnb9O5XoA1916It_apCSQVJmB6cCgxbxHbKiOU';
</code></pre>

!SLIDE
.notes than if there is an existing token that matches most of the start of the submitted token.

<pre style="font-size: 40px; margin-top: 10px;"><code>
SELECT id FROM accounts
WHERE password_reset_key = '<b>hJxZkHnb9O5XoA1916It_apCSQVJmB6cCgxbx</b>HbKiOU';
</code></pre>

!SLIDE
.notes Exploiting timing attacks over the internet is fairly difficult, especially in this case as the different in speed is very low and subject subtantial variance in general use. Because of those facts, this is mostly a theoretical vulnerability.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Still Mostly Theoretical</h1>

!SLIDE
.notes Another difference between Rodauth and other authentication libraries is Rodauth uses separate tables to store each separate type of token, instead of storing all token data as separate columns in a single table.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Separate Tables Per Feature</h1>

!SLIDE
.notes For example, if we go back to the Rodauth query I showed earlier, note that the table name is selects from is specific to password reset tokens.

<pre style="font-size: 40px; margin-top: 10px;"><code>SELECT key FROM <b>account_password_reset_keys</b>
WHERE id = 1234;
</code></pre>

!SLIDE
.notes This is not really for security, it's for performance.  If you have 10 million accounts, and only 1 thousand have password reset tokens at any one time, it's better for performance to have only 1000 rows of tokens, compared to having mostly NULL values for 10 million rows.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Performance</h1>

!SLIDE
.notes In review, Rodauth uses account specific tokens stored in separate tables, mostly for performance, but also for additional theoretical security.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Token Storage</h1>
<div class="review">Review</div>

!SLIDE
.notes Next I'm going to talk about Rodauth features.  Rodauth attempts to support most common authentication and account management features.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Features</h1>

!SLIDE
.notes Rodauth does not load any features by default.  You need to explicitly load each feature you want to use.  If you aren't using a feature, you don't have to pay any memory or performance cost for that feature.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Only Pay For What You Use</h1>

!SLIDE
!SLIDE
.notes The most commonly used features are login and logout.  Most of the discussion of password hash security related to how to authenticate users during login.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Login/Logout</h1>

!SLIDE
.notes There is a feature that allows users to change their password.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Change Password</h1>

!SLIDE
.notes There is a feature that allows users to change their logins.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Change Login</h1>

!SLIDE
.notes If you have users that forget their passwords, you can enable a feature to allow them to automatically reset their passwords.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Reset Password</h1>

!SLIDE
.notes You can also turn on a feature that supports users creating their own accounts.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Create Account</h1>

!SLIDE
.notes As well as a feature that requires the verify that they have access to the email address associated to the account.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Verify Account</h1>

!SLIDE
.notes There is a feature for automatically logging users in based on a token stored in a cookie, keeping track of whether they logged in, asking them to reauthenticate via password for security sensitive actions.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Remember Me</h1>

!SLIDE
.notes To prevent bruteforce attacks, there is a an account lockout feature that locks accounts out after a certain number of failed logins.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Account Lockout</h1>

!SLIDE
.notes Then final currently support feature allows users to close their own accounts.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Close Account</h1>

!SLIDE
.notes In review, Rodauth is a full featured authentication and account management framework, which should be able to handle the needs of most applications.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Features</h1>
<div class="review">Review</div>

!SLIDE
.notes Rodauth is fairly unique amongst authentication libraries in that it is configured using a block based DSL that allows overriding all parts of the framework.  This configuration approach is what makes Rodauth flexible enough to support almost all existing authentication needs.|So far, I've spent this entire presentation talking about Rodauth and security, without showing how to actually use it.  Let's fix that now.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Configuration</h1>


!SLIDE
.notes Rodauth is implemented as a plugin to the Roda web framework. So this is basically the minimal usage of Rodauth in the application. So if you are using Roda, you can use it directly in your application as a plugin.  If you aren't using Roda, I'll discuss how to use Rodauth in the next section.

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

!SLIDE
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

!SLIDE
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
.notes Since this section is focused on configuration, let's focus just on the configuration block

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
end
</code></pre>

!SLIDE
.notes I mentioned earlier that rodauth does not load any features by default.  In Rodauth, all features you want to use must be specified explicitly.  You do this by calling enable with symbols for the features you want to use.  In this case, they are the login and logout features.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  <b>enable :login, :logout</b>
end
</code></pre>

!SLIDE
.notes Each feature you load adds configuration methods related to the feature.  So after loading the login feature, you can call the after_login configuration method with a block to specify additional behavior to perform after a successful login.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  after_login do
    LOGGER.info "#{account.email} logged in from #{request.ip}!"
    super
  end
end
</code></pre>

!SLIDE
.notes In this case, we are recording successful logins to a log. The interesting things here are the call to account and request.  You see, each of these configuration blocks is evaluated in the context of a Rodauth object.  This object has access to everything related to the request.  So you can tailor how Rodauth will handle requests using any information related the request.  This design is what makes Rodauth flexible enough to handle most authentication needs.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  after_login do
    LOGGER.info "#{<b>account</b>.email} logged in from #{<b>request</b>.ip}!"
    super
  end
end
</code></pre>

!SLIDE
.notes For simplicity, Rodauth allows you to use arguments for many simple configuration settings.  For example, the account_model method sets the model to use for the account.  You can specify this by just passing the class to account model.  This is a useful shortcut.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  <b>account_model User</b>
end
</code></pre>

!SLIDE
.notes However, you can pass a block to account_model to override if you want to be able to change which account model to use based on a request parameter.  This ability to specify a block to override behavior and give complete control is how Rodauth is able to achieve its flexibility.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  account_model do
    if <b>request.params['admin']</b>
      <b>Admin</b>
    else
      <b>User</b>
    end
  end
end
</code></pre>


!SLIDE
.notes I will describe briefly how this is accomplished internally, though I will simplify it a bit.  The context in which the blocks are evaluated is an instance of a class we'll call Auth.

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
.notes And when you call one of the configuration methods with a block, you are actually defining an instance method in the Auth class.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Auth
  include Login
  include Logout

  def account_model
    if request.params['admin']
      Admin
    else
      User
    end
  end
end
</code></pre>

!SLIDE
.notes The configuration DSL is mainly a shortcut to create an authentication class that includes correct modules and has certain methods overridden as needed.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  account_model do
    if request.params['admin']
      Admin
    else
      User
    end
  end
end
</code></pre>

!SLIDE
.notes One consequence of this approach is that in all blocks given to configuration methods, you can call super to get Rodauth's default behavior.  The only issue here is because you are defining methods via a block, you must pass explicit arguments to super, which in this case is no arguments.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  account_model do
    if request.params['admin']
      Admin
    else
      <b>super()</b>
    end
  end
end
</code></pre>

!SLIDE
.notes Another interesting aspect of Rodauth's internals is that while I mentioned that features like Login and Logout and modules, that's only part of the story.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>class Auth
  include <b>Login</b>
  include Logout
end
</code></pre>

!SLIDE
.notes They are not modules in the sense that they aren't defined with the module keyword.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code><b>module</b> Login
end
</code></pre>

!SLIDE
.notes Rodauth actually has a class called Feature, which all of the features are instances in.  

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
  <b>def depends(&42;d)</b>
    dependencies.concat(deps)
  end
end
</code></pre>

!SLIDE
.notes Those instance methods are then callable as module methods when creating your feature.  Here we call the depends method, to set that the lockout feature depends on the login feature.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>Lockout = Feature.new do
  <b>depends :login</b>
end
</code></pre>

!SLIDE
.notes In review, Rodauth is configured via a simple but very flexible DSL, which allows it to handle the authentication and account management needs for most applications.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Configuration</h1>
<div class="review">Review</div>


!SLIDE
.notes I'd now like to show you how you can easily integrate Rodauth into your applications.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Integration</h1>


!SLIDE
.notes First, I want to go over how Rodauth can be used in web applications that don't use the Roda web framework.  It turns out this is actually very simple.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Other Web Frameworks</h1>

!SLIDE
.notes Roda itself supports a middlware plugin, which allows the application to be used as Rack middleware. So to use Rodauth in another web framework, you just create a small Roda app, load the middlware and rodauth plugins, and have the route block call r.rodauth.  Any route that Rodauth doesn't handle will be passed to the application.|You may be concerned about the extra overhead, but fear not, Roda is very light framework, and this only adds about 2.2MB to your application's memory overhead.  

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'roda'

class RodauthApp &lt; Roda
  <b>plugin :middleware</b>
  plugin :rodauth do
    enable :login, :logout
  end
  route do |r|
    r.rodauth
  end
end

use RodauthApp
</code></pre>

!SLIDE
.notes For database access, Rodauth uses Sequel internally, but you can certainly use ActiveRecord in your application and still use Rodauth. You would have to setup a Sequel database connection and create a model for the accounts, but that's pretty much it.|Sequel is not as small as Roda, but it still adds less than 9MB to your application's memory overhead.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>require 'sequel'
<b>Sequel::Model.db = Sequel.connect('postgres://...')</b>

plugin :rodauth do
  enable :login, :logout
  <b>account_model Class.new(Sequel::Model(:users))</b>
end
</code></pre>

!SLIDE
.notes If you aren't using PostgreSQL but still want to use Rodauth for authentication, or you have a legacy system that stores passwords in the same table as accounts, I mentioned earlier that Rodauth supports that.  You can enable it with a single configuration method called account_password_hash_column, specifying which column contains the password hash.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  <b>account_password_hash_column :password_hash</b>
end
</code></pre>

!SLIDE
.notes What if you want to authenticate using LDAP, say to Windows Active Directory?  That can be accomplished using a single configuration method.  You just call the password_match? configuration and pass it a block.  Using the already retrieved account and the provided password, you can use any custom authentication method that you want.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  <b>password_match? do |password|</b>
    <b>SimpleLdapAuthenticator.valid?(account.username, password)</b>
  end
end
</code></pre>

!SLIDE
.notes What if you want to have different authentication behavior for different sections of your application?  For example, if you want to separate admin account authentication from other accounts.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
end

plugin :rodauth, :name=>:admin do
  enable :login, :logout, :change_password
  account_model AdminAccount
  password_hash_table :admin_password_hashes
end
</code></pre>

!SLIDE
.notes Rodauth supports this.  You can load the Rodauth plugin multiple times with different names to store different rodauth configurations.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
end

plugin :rodauth, <b>:name=>:admin</b> do
  enable :login, :logout, :change_password
  account_model AdminAccount
  password_hash_table :admin_password_hashes
end
</code></pre>

!SLIDE
.notes Then, in your routing tree block, you can call r.rodauth to use the default configuration.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>route do |r|
  <b>r.rodauth</b>

  r.on "admin" do
    r.rodauth(:admin)
  end
</code></pre>

!SLIDE
.notes But in the admin section of your site, you can call r.rodauth with the symbol admin, which will use the admin configuration.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>route do |r|
  r.rodauth

  <b>r.on "admin"</b> do
    <b>r.rodauth(:admin)</b>
  end
</code></pre>

!SLIDE
.notes Let's take a brief look at a real world example of Rodauth usage.  This is from one of my open source applications called Giftsmas, which is a gift tracking program.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  account_model User
  skip_status_checks? true
  account_password_hash_column :password_hash
  title_instance_variable :@title
end
</code></pre>

!SLIDE
.notes As I showed earlier, the first step is to enable the the required features, in this case login and logout.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  <b>enable :login, :logout</b>
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  account_model User
  skip_status_checks? true
  account_password_hash_column :password_hash
  title_instance_variable :@title
end
</code></pre>

!SLIDE
.notes Giftsmas was first created in 2008, and its authentication system wasn't converted to Rodauth until last year.  These methods all override the default values so that the user interface for Giftsmas didn't change.  No changes were required to giftsmas integration tests when converting it to Rodauth.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  <b>session_key :user_id</b>
  <b>login_param 'user'</b>
  <b>login_label 'User'</b>
  <b>login_column :name</b>
  <b>account_model User</b>
  skip_status_checks? true
  account_password_hash_column :password_hash
  <b>title_instance_variable :@title</b>
end
</code></pre>

!SLIDE
.notes Before I converted Giftsmas to use Rodauth, it already stored bcrypt password hashes in a column in the same table, so Giftsmas uses the account_password_hash_column configuration method.  I run the Giftsmas demo site on Heroku, so I can't really switch it to do Rodauth's more secure default approach.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  account_model User
  skip_status_checks? true
  <b>account_password_hash_column :password_hash</b>
  title_instance_variable :@title
end
</code></pre>

!SLIDE
.notes Finally, Giftsmas doesn't have account statuses, as all user accounts are considered active, so it tells the configuration to skip the account status checks.

<pre class="sh_ruby" style="font-size: 40px; margin-top: 10px;"><code>plugin :rodauth do
  enable :login, :logout
  session_key :user_id
  login_param 'user'
  login_label 'User'
  login_column :name
  account_model User
  <b>skip_status_checks? true</b>
  account_password_hash_column :password_hash
  title_instance_variable :@title
end
</code></pre>

!SLIDE
.notes Hopefully these examples show how Rodauth can be integrated easily into existing applications, due to how flexible its configuration is.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Integration</h1>
<div class="review">Review</div>

!SLIDE
.notes Security, Simplicitity, and Flexibility.  These are Rodauth's three design goals.

<div class="review">Design Goals</div>
<h1 style="font-size: 130px; margin-left: 20px; margin-top: 80px;">Security</h1>
<h1 style="font-size: 130px; text-align: right; margin-right: 20px; margin-top: 30px;">Simplicity</h1>
<h1 style="font-size: 130px; margin-left: 20px; margin-top: 30px;">Flexibility</h1>

!SLIDE 
.notes If you value those goals and want to learn more about Rodauth, check out rodauth.jeremyevans.net for details.

<h1 style="text-align: left; margin-left: 50px; margin-top: 580px; font-size: 70px; color: white;">http://rodauth.jeremyevans.net</h1>

!SLIDE
.notes I'd like to end this presentation by discussing some ideas I'd like to take with you, not specifically related to Rodauth.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Takeaways</h1>


!SLIDE
.notes First, prefer longer passwords over shorter but more complex passwords.  As I showed earlier, longer passwords can be significantly more secure than shorter but more secure passwords.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Prefer Longer Passwords</h1>

!SLIDE
.notes Second, if you store passwords, always use bcrypt or scrypt as the password hash.  For important passwords, such as administrative passwords, consider using a higher than default cost factor.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Use bcrypt/scrypt</h1>


!SLIDE
.notes Third, I'd like you to consider using multiple database accounts in your applications where it makes sense from a security perspective.  I rarely see applications that do this.  While it does take some additional work to setup, if security is important, the benefits of doing so should outweigh the costs.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Multiple Database Accounts</h1>


!SLIDE
.notes Fourth, recognize that database tables are cheap.  In reviewing other authentication frameworks and other applications, I see a tendency to use tables with many NULLable columns, which is usually considered a smell from a database design perspective.  Database tables are not a limited resource, don't be afraid to use more of them.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Database Tables Are Cheap</h1>


!SLIDE
.notes Finally, if you have an idea for creating a library, look into existing libraries that due the same or similar things.  Before I started work on Rodauth, I looked at Devise, Authlogic, and Sorcery.  Originally, my goal was to see if any of them would be flexible enough to handle the needs in all of my applications, and would be possible to use without Rails.  Ultimately, I ended up writing Rodauth, but the knowledge I gained from reviewing existing libraries was very helpful to me in doing so.

<h1 style="font-size: 300px; position: absolute; top: 180px; left: 20px;">Read Existing Libraries</h1>


!SLIDE 
.notes That concludes my presentation.  I would like to thank the organizers for inviting me to speak to all of you today, as well as thank all of you for listening to me.

<h1 style="text-align: left; margin-left: 50px; margin-top: 580px; font-size: 70px; color: white;">Thank You</h1>

!SLIDE 
.notes If any of you have questions, I'll be happy to answer them now.

<h1 style="text-align: left; margin-left: 50px; margin-top: 580px; font-size: 70px; color: white;">Questions?</h1>
