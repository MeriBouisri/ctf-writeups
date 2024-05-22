# Etoiles 1 

## Introduction

### Description
```
Petite Marie, allons regarder les étoiles, le ciel est si clair et on voit tellement d'étoiles... 
La voute étoilée est incomparable dans les iles grâce à l'absence de pollution lumineuse, 
surtout qu'il n'y a plus de courant depuis une semaine et que les groupes électrogènes sont à sec.

Extraire le message en clair de ciel.txt à l'aide de etoile.c
```

`Link` : [Etoiles 1](https://ctf.hackin.ca/challenges#%C3%89toiles%201%20%E2%AD%90-1)
`Points` : 100
`Category` : Reverse engineering

## 1. Analysis

I was unable to open the `etoile.c` file to check the contents. I changed the extension to `etoile.txt` to be able to open it.
The file has been obfuscated with useless comment lines, bringing the contents to 102379 lines.

Trying to compile the file with `gcc` takes too long. We can try to clean up the code.

## 2. Solution

### 2.1 Clean up code

#### 2.1.1 Remove useless lines of code

This will allow us to work with a more manageable size.

In sequence (vim)

```
:21, 20467d
:29, 20443d 
:32, 20461d
:134, 20537d 
:178, 20502d 
```

We can now change the extension of the file back to `etoile.c`

### 2.1.2 Fix pointers

When trying to compile the program after all this, it will give output several errors (and notes/warnings, but those arent important). 
This is the first error :

```
clean_01.c: In function ‘pop’:
clean_01.c:140:80: error: invalid type argument of unary ‘*’ (have ‘struct star’)
  140 | *result/*********************************************************************/=*
      |                                                                                ^
  141 | heap/**************************************************************************/
      | ~~~~

```

Thats because, while cleaning up the code at step (2.1), we accidentally delete a ** thats included in the code.

In the raw code :

```c
/****************************************************************************/**
heap,int*/*********************************************************************/
heaplen)/**********************************************************************/
```

After the cleanup :

```c
pop/***************************************************************************/
(/***********************************************************************/struct
/**************************************************************************/star
heap,int*/*********************************************************************/
heaplen)/**********************************************************************/
```

So, we must add `**` to finish our function :

```c
pop/***************************************************************************/
(/***********************************************************************/struct
/**************************************************************************/star**
heap,int*/*********************************************************************/
heaplen)/**********************************************************************/
```

### 2.2 Compiling the program

The program can now be compiled :

```bash
gcc etoile.c -o etoile
```

This command will compile the cleaned up `etoile.c` source file and output an executable file `etoile`. 
We can now run the program on the `ciel.txt` file 

```
./etoile ciel.txt
```

This will output the image, and the flag

```
Le message lu dans le ciel : JFFI{loOK&S3e}
```

You can find the complete cleaned up code in the file `etoile_fixed.c` of this repo.


## 3. Flag

```
JFFI{loOK&S3e}
```



