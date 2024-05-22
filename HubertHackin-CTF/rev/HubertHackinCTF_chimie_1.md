# Chimie 1

## Introduction

### Description
```
J'ai retrouvé mes notes de chimies. J'avais noté des informations importantes dessus mais je ne les vois pas."
```

`Link` : [Chimie 1](https://ctf.hackin.ca/challenges#Chimie%201-163)

`Points` : 50

`Category` : Reverse engineering

## 1. Analysis

We are given a file called `chimie1.ps`. 

### 1.1 File extension .ps

The file extension of the provided file is `.ps`, the extension for PostScript files.
In the file, a link to a course for PostScript is given. However.

Inside the file, the flag is encoded, and the decoding function is given.

```ps
/flag1 < 606d6f6451524658754f46435e745a484f74444857 > def
/getkeyelem {
    2 copy pop length
    mod
    get
} def
/encode {
    0 
    1 
    3 index length 1 sub 
    {
        2 index exch
        dup
        2 index exch
        get
        1 index
        4 index exch getkeyelem
        xor
        put
    }
    for
    clear
} def
flag1 key1 encode
```

At the end of the program, the flag is show :

```ps
x 2000 sub y moveto flag1 show
```


## 2. Solution

### 2.1 Displaying the .ps file

It is possible to display the output of a PostScript program from the command line, thanks to `GhostScript`.

```
$ apt install ghostscript
$ gs chimie1.ps
```

This will the contents of with `chimie1.ps`. This will display the other contents, but not the flag. 
That's because it is drawn outside of the frame.

In order to see the flag, we can modify the last line of the program from this :

```
x 2000 sub y moveto flag1 show
```

To this :

```
x y moveto flag1 show
```

Which will display the flag just under the chemistry equation.

## 3. Flag

```
JFFI{you_dont_see_me}
```



