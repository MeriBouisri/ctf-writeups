# Chimie 2

## Introduction


**Link** : [Chimie 2](https://ctf.hackin.ca/challenges#Chimie%202-161)

**Points** : 100

**Category** : Reverse engineering

#### Problem

> J'ai retrouvé mes notes de chimies. 
> J'avais noté des informations importantes dessus 
> mais je ne les vois pas.

## Solution

We are given a file called `chimie2.ps`. 

### 1. Analysis

Just like `Chimie 1`, the flag is encoded and the decoding function is given.
However, this time, the flag is in the form of an array.

```ps
/key2 < abcdef > def
/flag2 [ 225 139 169 226 182 156 223 172 140 192 146 141 202 190 138 207 146 140 202 163 176 201 168 176 223 191 134 200 166 150 244 185 128 244 191 138 202 169 146 ] def
```

Trying to display the flag like in the last challenge outputs an error, since it is not a string.

### 2. Displaying the flag

#### 2.1 Printing the contents of the decoded flag

By reading the program, we see that the flag has been encoded and the data is in memory.
To access it, we can run `gs` with `chimie2.ps` :

```bash
$ gs chimie2.ps
```

From the command line, we can access the data from the `flag2` variable like this :

```
GS> flag2 pstack
```

Which will output the following :

```
[74 70 70 73 123 115 116 97 99 107 95 98 97 115 101 100 95 99 97 110 95 98 101 95 116 114 105 99 107 121 95 116 111 95 114 101 97 100 125]
```

The elements appear to be numbers between 0-255, which means they might be the decimal representation of ascii characters.

#### 2.2 Decode decimal to ASCII

We can use an online decoder like [this one](https://www.dcode.fr/ascii-code), or do it from the command line :

```bash
arr=(74 70 70 73 123 115 116 97 99 107 95 98 97 115 101 100 95 99 97 110 95 98 101 95 116 114 105 99 107 121 95 116 111 95 114 101 97 100 125); 
for i in "${arr[@]}"; do 
    echo -ne \\x$(printf %02x $i); 
done
```

Which will output the flag :

```
JFFI{stack_based_can_be_tricky_to_read}
```

## Flag

```
JFFI{stack_based_can_be_tricky_to_read}
```



