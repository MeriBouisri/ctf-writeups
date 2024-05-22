# Spag

## Introduction

**Link** : [spag](https://ctf.hackin.ca/challenges#spag-160)

**Points** : 100

**Category** : Reverse engineering

#### Problem

> J'aime les spaghetti avec du fromage, beaucoup de fromage. 
> Et beaucoup de spaghetti.
>
> java -jar Fromage.jar 'JFFI{MonFlag}'

## Solution

We are given a file called `Fromage.jar`. 

### 1. Analysis

#### 1.1 Extracting the jar file

A jar file is a compressed file that contains the java source code. We can unzip it like any compressed file :

```
$ unzip Fromage.jar
```

This will extract all the files in the current directory.

#### 1.2 Fix the encoding (Optional)

It is difficult the recompress the program into a jar file because of the invalid encoding, caused by accents in class name (like `Comté.java`)
I chose to simply rename the classes to something valid.

#### 1.3 Understanding the program

For the program to end at some point, there must be an end point where a certain method `miam(String, int)` must return `true`.
Since there is only one possible flag, there must also be one single sequence of calls that corresponds to this flag.

We know that the flag starts with `JFFI{` and ends with `}`. The class that is called once the last character is reached must return true.

The sequence of calls (from caller to callee) is the following, for the known characters :

```
Comte -> Tomme -> SaintNectaire -> Raclette -> Beaufort
```

Which spells out `JFFI{`

### 2. Finding the correct sequence

I start by looking for a possible end, and I eventually find it in `Roquefort.java` :

```java
public class Roquefort extends Fromage {
    public static boolean miam(String s, int i) {
        if (i>=s.length()) return true;
        // ...
    }
}
```

We can now work our way back by finding all references to `Roquefort` where it is associated with the last character of the flag, `}`.

```java
public class MothaisSurFeuille extends Fromage {
    public static boolean miam(String s, int i) {
        if (i>=s.length()) return false;
        switch (s.charAt(i)) {
            // ...
		    case '}': return Roquefort.miam(s, i+1); 
        }
    }
}
```

We must know find the references to `MothaisSurFeuille`, and proceed this way until a known character is reached (`JFFI{`),
while disqualifying the classes that we have already passed, and the classes that reference the class we're looking for more than once.

The final sequence is this, starting from the end (From callee to caller) :

```
Roquefort -> MothaisSurFeuille -> Laguiole -> ChausseeAuxMoines -> Boursin -> FourmeDAmbert -> Vacherin* 
-> Emmental -> Rocamadour* -> SaintFelicien -> Munster -> Chabichou -> Abondance -> Cantal -> Morbier -> SaintMarcellin -> MontDOr 
-> Reblochon -> Camembert -> BrieDeMeaux -> Beaufort -> Raclette -> SaintNectaire -> Tomme -> Comte

*Caution! Multiple possible classes reference this one. You might have to try some of them to determine a valid sequence)

```

Following this sequence gives us the following characters, in correct order

```
JFFI{LEFROMAGECESTLAVIE}
```

## Flag

```
JFFI{LEFROMAGECESTLAVIE}
```



