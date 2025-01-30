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

