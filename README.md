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