## 1️⃣️Bandit0

To connect remotely:

```ssh -p [port] [username]@[hostname] ```

for example

``` ssh -p 2220 bandit0@bandit.labs.overthewire.org```

One is then prompted for the password.

## Bandit1

The password is in a folder in root 

Therefore to go to root : ``` cd ~ ```

Then: ```ls```

After listing the files in the directory ```cat <filename>```

## Bandit2

The file name in the home folder is - , a special character


'-' is interpreted as stdin/stdout 

To view its contents use redirection:

```cat < -```

or the complete path ```cat ./-```

or use ```more - ```



Also,  the rev command doesn't treat - as a special character.


"The rev utility copies the specified files to standard output, reversing the order of characters in every line."

so this also works ```rev - | rev```

## Bandit3

To view filenames with spaces, write the first few characters then press tab to fill the rest of the characters; this automatically escapes the spaces.

One can also escape them manually:

```cat "sample\ file"```

Another option is to wrap the name with quotes. 

For example:

``` cat "sample file"```

## Bandit4

To find the hidden file use:

```bash
ls -a
```
## Bandit5

To find the file and password. One can use:

```bash
file -- *
```

The ```--``` ensures that any file that starts with ```-``` is not treated as an option.

The command finds the file type, then use redirection to see the content of the only listed human readable file:

```bash
cat < -filename
```
## Bandit6

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


## Bandit7

To find the file with the password, the ```find``` command becomes useful. 

We start the search from the root. However, it is important to note that some directories might  be restricted therefore to avoid the errors from being printed we direct any errors form the stderr(2) to ```/dev/null``` 

The ```/dev/null``` is like a blackhole in linux and unix-based systems. So anything passed to it disappears. 


so the command will be:

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

## Bandit8

To find the password for level 8, ```grep``` is important. It helps in finding matches. 

One can use this:

```bash
cat data.txt | grep millionth```
```

or 

```bash
grep millionth data.txt
```

## Bandit9

The ```uniq``` can help one find only set of characters that appear only once using the unique option `-u`. That is:

```uniq -u```

Option `-d` lists the duplicates and `-c` the
count.

However to do so the data should be sorted then piped to uniq:

```bash
sort data.txt | uniq -u
```

## Bandit 10

To find the password use `strings` since it returns human readable characters. Piping it to `grep` can then help find the password preceded by the `=` sign.

Therefore the command is:

```bash
strings data.txt | grep '=='
```

## Bandit 11

Base64 helps to represent binary data in ASCII.

In order to encode use:

```bash
base64 <<< 'Hello, World1'
```

Also to decode use

```bash
base64 -d <<< VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==
```
## Bandit 12

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
