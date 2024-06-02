# Le Lac de l'Ombre 2 

## Introduction

**Link** : [Le Lac de l'Ombre 2](https://ctf.hackin.ca/challenges#Le%20Lac%20de%20l'Ombre%202%20%F0%9F%90%89-220)

**Points** : 30

**Category** : Reverse engineering

#### Problem

>La menace menace encore les villageois. Mettez-y un terme une bonne fois pour toute.
>
> Pechez le second flag.
> ncat --ssl lacdelombre.chals.hackin.ca 443 

## Solution

We are given a file called `LacDeLOmbre.java`. Please check the writeup `HubertHackinCTF_LeLacDeLOmbre_1.md` for more info.

### 1. Analysis

#### 1.1 Flag endpoint

In order to get the second flag, we must assign an instance of `SerpentGeant` to `prise` at the third attempt (idx=2)

```java
class SerpentGéant extends Animal {
	SerpentGéant() { valeur = 10; }
	public Animal maisEnVrai(int tentative) {
		if (tentative==0) return new Écrevisse().enlarge(3);
		if (tentative==2) return new Anguille().enlarge(6);
		return this;
	}
	public void apres3Mois() {
		System.out.println(Flag.FLAG2);
	}
}
```

However, calling `maisEnVrai(int tentative)` on the file attempt will return an `Anguille()` object.

#### 1.2 Assigning the correct object

We have to try and find vulnerabilities during the input and before `prise` is assigned to anything else after the second attempt, specifically this part of the code :

```java
Animal attrapeEnUn() throws Exception {
    System.out.println(labels[tentative]);
    BufferedReader buffer = new BufferedReader(new InputStreamReader(System.in));
    do {
    ligne = buffer.readLine().trim();
    } while (ligne.equals(""));
    // ...
}
```

Generating an error here will allow us to avoid overwriting the value in `prise` during the third attempt, which means that the program will continue while keeping the value assigned during the second attempt.


### 2. Generating an error

#### 2.1 BufferedReader vulnerability

While researching a bit, I found a potential vulnerability thanks to this forum :

- [BufferedReader.readLine() can return null on EOF](https://teamtreehouse.com/community/bufferedreaderreadline-can-return-null-on-eof)

The error that we can generate from the user input is a `java.land.NullPointerException`, caused by `buffer.readLine()` returning a null value. 

To do so, during the input, the user can press `<Ctrl-D>` which will correspond to the character `^D`. 

Doing this will invoke the following error :

```
Erreur: java.lang.NullPointerException: Cannot invoke "String.trim()" because the return value of "java.io.BufferedReader.readLine()" is null
```

#### 2.2 Exploiting the vulnerability

Thanks to this vulnerability, we can reach the third attempt without overwriting the value from the second attempt. 

The sequence of input will be the following (`truite > serpent géant > Ctrl-D`) :

```
Que tentez-vous de pécher le premier mois?
> truite
Et le second mois?
> serpent géant
Et enfin le troisième mois?
Erreur: java.lang.NullPointerException: Cannot invoke "String.trim()" because the return value of "java.io.BufferedReader.readLine()" is null
INF600C{attendez_il_faut_que_ca_soit_vrai_tout_ce_qu_on_dit_la}

```

## Flag

```
INF600C{attendez_il_faut_que_ca_soit_vrai_tout_ce_qu_on_dit_la}
```
