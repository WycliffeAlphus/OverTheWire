## Bandit 0

To connect remotely:

```ssh -p [port] [username]@[hostname] ```

for example

``` ssh -p 2220 bandit0@bandit.labs.overthewire.org```

One is then prompted for the password.

## Bandit 0 > 1

The password is in a folder in root 

Therefore to go to root : ``` cd ~ ```

Then: ```ls```

After listing the files in the directory ```cat <filename>```

## Bandit 1 > 2

The file name in the home folder is - , a special character


'-' is interpreted as stdin/stdout 

To view its contents use redirection:

```cat < -```

or the complete path ```cat ./-```

or use ```more - ```



Also,  the rev command doesn't treat - as a special character.


"The rev utility copies the specified files to standard output, reversing the order of characters in every line."

so this also works ```rev - | rev```

## Bandit 2 > 3

To view filenames with spaces, write the first few characters then press tab to fill the rest of the characters; this automatically escapes the spaces.

One can also escape them manually:

```cat "sample\ file"```

Another option is to wrap the name with quotes. 

For example:

``` cat "sample file"```

## Bandit 3 > 4

To find the hidden file use:

```bash
ls -a
```
## Bandit 4 > 5

To find the file and password. One can use:

```bash
file -- *
```

The ```--``` ensures that any file that starts with ```-``` is not treated as an option.

The command finds the file type, then use redirection to see the content of the only listed human readable file:

```bash
cat < -filename
```
## Bandit 5 > 6

To find the only file with the specidfied size use:

```bash
find . -size 1033c
```
or 

```bash

find . -size 1033c ! -executable
```
or

```bash
find . -size 1033c ! -executable -exec file {} +
```

The redirect the contents of the files to ```cat```


## Bandit 6 > 7

To find the file with the password, the ```find``` command becomes useful. 

We start the search from the root. However, it is important to note that some directories might  be restricted therefore to avoid the errors from being printed we direct any errors form the stderr(2) to ```/dev/null``` 

The ```/dev/null``` is like a blackhole in linux and unix-based systems. So anything passed to it disappears. 


so the command will be:

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

## Bandit 7 > 8

To find the password for level 8, ```grep``` is important. It helps in finding matches. 

One can use this:

```bash
cat data.txt | grep millionth```
```

or 

```bash
grep millionth data.txt
```

## Bandit 8 > 9

The ```uniq``` can help one find only set of characters that appear only once using the unique option `-u`. That is:

```uniq -u```

Option `-d` lists the duplicates and `-c` the
count.

However to do so the data should be sorted then piped to uniq:

```bash
sort data.txt | uniq -u
```

## Bandit 9 > 10

To find the password use `strings` since it returns human readable characters. Piping it to `grep` can then help find the password preceded by the `=` sign.

Therefore the command is:

```bash
strings data.txt | grep '=='
```

## Bandit 10 > 11

Base64 helps to represent binary data in ASCII.

In order to encode use:

```bash
base64 <<< 'Hello, World1'
```

Also to decode use

```bash
base64 -d <<< VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==
```
## Bandit 11 > 12

To get back a rot13 encoded string, use the `tr` command. It lets one subtitute characters with another. So the solution will be:

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

tr `A-Za-z` `N-ZA-Mn-za-m`

`A-Za-z`: This defines the set of all uppercase (A-Z) and lowercase (a-z) letters.

`N-ZA-Mn-za-m`: This defines the transformed set where:
        
```A-M shifts to N-Z```

```N-Z shifts back to A-M```

```a-m shifts to n-z```

```n-z shifts back to a-m```

This mapping effectively shifts each letter 13 places forward in the alphabet wrapping around at the end.
 
 ## Bandit 12 > 13

 To solve this level, creat a directory `temp` and a random one inside `temp` using:

 ```bash
 mktemp -d
 ```

Then when inside the directory use:

```bash
cp ~/data.txt
```

It helps copy the file from home (~) to the current directory.

Revert the hexdamp using:

```bash
xxd -r <hexdump> <new file name>
```

After that it is a series of renaming and decompressing.

When `cat hexdump` is used it the header shows that it is of gzip type, `00000000: 1f8b 0808`. 

To decopmress it is renamed to `.gz`

```bash
mv filename filename.gz
```

Then use:
```bash
gzip -d filename.gz
```

The file then becomes `BZIP2`

```bash
00000000: 425a 6839
```
Therefore it is renamed to `.bz2` 
 
 ```bash
 bzip2 -d filename.bz2
 ```

 xxd shows that it goes back to `gzip` compression

 Therefore renaming to `.gz` then 

 ```bash
 gzip -d filename.gz
 ```

 When `cat` is used on the decompressed file, it shows an archive: `data5.bin`

 The .bin is a tar file, decompressing it needs changing the name to `.tar`. Then use:

 ```bash
 tar -xf filename.tar
 ```

 ls then shows `data5.bin` as one of the files and it has an archive `data6.bin`

 extract the file using:

 ```bash
 tar -xf data5.bin
 ```

 `data6.bin` is bzip2 compressed if one uses ```xxd data6.bin```. 

 One can use :

 ```bash
 bzip2 -d data6.bin
 ```

 It leads to `data6.bin.out` which has an archive `data8.bin`

 ```bash
 tar -xf data6.bin.out
 ```
 We now have `data8.bin` which is a `gzip` . It is confirmed using `xxxd data8.bin`

 After renaming to `.gz` and extracting the contents one gets a readable file:

 ```bash
 gzip -d data8.gz
 ```

 Then:

 ```bash
 cat data8
 ```

## Bandit 13 > 14

To go to the next level we need to used a sshkey. Doing so requires the sshkey to be in the local machine. 

Since the key is available in bandit13  we can `scp` command to retreive it.

It uses ssh to transfer data over the network and syntax is:

```bash
scp -p <port> <user>@<IP>:<remotefilepath><localfilepath>
```

I used this:

```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .
 ```
 The file was saved in my present (.) directory.

 I then used it to log into level 14:

 ```bash
ssh -i sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org

 ```      


 `-i` is used to specify an identity file (private key) for authentication when connecting to a remote server.  

 ## Bandit 14 > 15

 This level requires one to submit the password of the current level to port 30000 on localhost. 

 first we retrieve the password using `cat`; the folder to find it was in the previous level.

```bash
cat /etc/bandit_pass/bandit14
```
Then make a connection and submit it:

```bash
nc localhost 30000
```

 ## Bandit 15 > 16

 To get the password for the next level, the `openssl` command is important; It is used while logged into bandit15.

 ```bash
 openssl s_client -connect localhost:30001
 ```

 `openssl s_client` connects to a server using SSL/TLS.

Submitting level15's password to the established connection leads to the retrieval of the needed password.


## Bandit 16 > 17

This level involves Port Scanning: finding open ports.

This can be done using `nmap` :

```bash
nmap -sV -p 31000-32000 localhost
```

`-sV` helps in detecting the service and version information from the open posts.
`-p` enables one to provide a port.

`localhost` is the host name which resolves to an ip address.

After identifying the port which speaks SSL and does not `echo` back info, we send the current password to it using `openssl`

```bash
echo '<password>' | openssl s_client -connect localhost:<port> -quiet
``` 

The `-quiet` option ensures that only response from the server is printed out: information such as certificate details, handshake process and cipher negotiation is ignored.


It returns a private key that can be used to ssh into the next level after storing it in a file: `private.key`

```bash
ssh -p 2220 -i private.key bandit17@bandit.labs.overthewire.org
```

After gaining access to level 17, capture the password and store it:

```bash
cat /etc/bandit_pass/bandit17
```

## Bandit 17 > 18

In this level the `diff` command is important.

It helps identify the change that has been made in the new file in order to get the password.

```bash
diff passwords.old passwords.new
```

## Bandit 18 > 19

To solve this level we add a command after the common ssh expression, first ls to check that the file with the password exists, then we used cat to check it.

```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org ls
```

```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org cat readme
```
## Bandit 19 > 20

Since the `suid` has been given to run the binary as the root or owner one can get the details of the password

`ls -la` gives this:

```bash
total 36
drwxr-xr-x  2 root     root      4096 Sep 19 07:08 .
drwxr-xr-x 70 root     root      4096 Sep 19 07:09 ..
-rwsr-x---  1 bandit20 bandit19 14880 Sep 19 07:08 bandit20-do
-rw-r--r--  1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--  1 root     root      3771 Mar 31  2024 .bashrc
-rw-r--r--  1 root     root       807 Mar 31  2024 .profile
```

It shows that the file is owned by bandit20 and belongs to bandit19 group, which has execution permissions on the file:

`setuid` is set for the file meaning the group can run it as root; also since it is small `s`, it means the execution permission is also set for the owner.

```bash
./bandit20-do ls /etc/bandit_pass
```
shows the password files for different levels, then print the 

password using:

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

## Bandit 20 > 21

This level involves connecting to the network daemon via the nc command.

It is importatn to start a connection in server mode to listen for inbound connections.

```bash
 echo -n 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO | nc -l -p 8081 &
 ```

 The `-n` flag ensures that no  newline is sent with the input

 The `nc -l -p 8081`  creates a connection in server mode - which listens for inbound connection.

 The `&` moves the process to the background

 Running the setuid binary with the same port will establish a connection to the netcat server which will send the password to it through `echo`. 

 The binary then answers with the next level's password.

```bash
 ./suconnect 8081
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
[1]+  Done                    echo -n 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO | nc -l -p 8081
```
## Bandit 21 > 22

The level uses cron jobs. 
We first find the jobs running in the directory

```bash
ls -la /etc/cron.d/
total 44
drwxr-xr-x   2 root root  4096 Sep 19 07:10 .
drwxr-xr-x 121 root root 12288 Sep 20 18:37 ..
-rw-r--r--   1 root root   120 Sep 19 07:08 cronjob_bandit22
-rw-r--r--   1 root root   122 Sep 19 07:08 cronjob_bandit23
-rw-r--r--   1 root root   120 Sep 19 07:08 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8  2024 e2scrub_all
-rwx------   1 root root    52 Sep 19 07:10 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31  2024 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
```

There is a cronjob that runs as bandit22 user, we can print its content to learn more:

```bash
cat /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```
we are able to see the script file, which we can cat into:

```bash
 cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

```
The output tells us that a file with everyone having read permissions is created:`/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`

the password for the level is then printed and the output redirected to the created file:

```bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
we can then print out its contents:

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```
## Bandit 22 > 23

This level also has a cronjob running in `/etc/cron.d/`

Therefore following the previous level's steps we can see that there is a cronjob running as user bandit23

```bash
ls -la /etc/cron.d
total 44
drwxr-xr-x   2 root root  4096 Sep 19 07:10 .
drwxr-xr-x 121 root root 12288 Sep 20 18:37 ..
-rw-r--r--   1 root root   120 Sep 19 07:08 cronjob_bandit22
-rw-r--r--   1 root root   122 Sep 19 07:08 cronjob_bandit23
-rw-r--r--   1 root root   120 Sep 19 07:08 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8  2024 e2scrub_all
-rwx------   1 root root    52 Sep 19 07:10 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31  2024 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
```

Using `cat` we see a script for bandit23

```bash
cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```
The script runs various commands:

```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

According to the script, the target is a file that is created using a set of commands, thus the myname variable can be substituted with `bandit23`:

```bash
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```
Using the generated file name one can then obtain the password:

```bash
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

## Bandit 23 > 24

This level involves creating a script that will be scheduled to run using a cron job.

```bash
 ls /etc/cron.d/
cronjob_bandit22  cronjob_bandit24  otw-tmp-dir
cronjob_bandit23  e2scrub_all       sysstat
```

we see that there is a cronjob for bandit24

Printing its contents result in:

```bash
cat /etc/cron.d/cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

Below we see that the cron checks for any scripts written by user 23, runs the them then deletes the scripts.

```bash
cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

we'll create a script that the cron we'll run to obtain the password and the file to save the password.

```bash
mkdir /tmp/rand
touch password
```

```bash
bandit23@bandit:~$ cd /tmp/rand
bandit23@bandit:/tmp/rand$ nano script.sh
```

The script:
```nano
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/rand/password
```

We then copy the script to where the cron job is:


```bash
cp script.sh /var/spool/bandit24/foo
```

We then wait for about a minute or so for the script to be executed and the password directed to `password`


```bash
bandit23@bandit:/tmp/rand$ cat password
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```
