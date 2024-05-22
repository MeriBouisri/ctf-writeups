# Etoiles 2 

## Introduction

### Description
```
Petite Marie, grâce au pouvoir de l'imagination, nous pouvons construire nos propres constellations 
et écrire nos propres messages dans le ciel... Essayons !

Fabriquez une image de ciel qui contient le message JeViensDuCiel. 
Validez votre image de ciel sur le serveur de validation d'images de ciel.
https://jffi24-etoile.chals.hackin.ca

```

`Link` : [Etoiles 2](https://ctf.hackin.ca/challenges#%C3%89toiles%202%20%E2%9C%A8-2)
`Points` : 101
`Category` : Reverse engineering

## 1. Analysis

I already cleaned up and fixed the source file that decodes a sky image.
We can now try to read it to understand what it does, and how to write an ascii image
that will output the correct message.

### 1.1 Understanding the source file

Even with the cleaned up code, it is hard to understand what it does. 

When running the program with the original `ciel.txt`, we can see that
the star character `*` is highlighted in yellow.

The code indicates that the characters are decoded from a range between a star to another.

```c
int main(int argc, char** argv) {
    // ...
	char msg[starlen] ;
	*(msg+starlen-1)='\0';
	for( int i =0;i<starlen-1;i ++) {
		*(msg+i)=astar( *(constellation+ i),* (1+i+constellation));
	}
	printsky();
	printf("Le message lu dans le ciel : %s\n" ,msg) ;
}
```

### 1.2 Modifying input and analyzing output

In the input file `ciel.txt`, we can try modifying the contents to see the effects on the input. We do that by removing or adding the `*`character.

As a reminder, the original output of `ciel.txt` is the flag for the challenge `Etoiles 1`, but we will only be interested in the first part :

```
gcc etoile_fixed.c -o etoile
./etoile ciel.txt

[Output] : JFFI{...}
```

Removing the first occurence of the `*` in `ciel.txt` will give the following output

```
[Output] : FFI{...}
```

And wrapping a character in the file `ciel.txt` with two stars (like so : `*c*`) will output the character between the stars :

```
[Output] : cFFI{...}
```

## 2. Solution

### 2.1 Creating the text file

During the analysis, we find out that the program will read characters in the path between two stars.
So, to pick a specific character, we can wrap it between two start characters `*[...]*`.

In order the create a file that outputs the correct message `JeViensDuCiel`, we create a new text file and put a `*` between each character :

This is the contents of the file :

```
*J*e*V*i*e*n*s*D*u*C*i*e*l*
```

Then, we can load the file to the website, as instructed. This will show the flag :

```
Message attendu bien reçu

JFFI{ImJustAPoorGirlINeedNoSympathy}
```

## 3. Flag

```
JFFI{ImJustAPoorGirlINeedNoSympathy}
```



