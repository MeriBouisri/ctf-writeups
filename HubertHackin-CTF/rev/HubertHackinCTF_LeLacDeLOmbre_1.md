# Le Lac de l'Ombre 1 

## Introduction

**Link** : [Le Lac de l'Ombre 1](https://ctf.hackin.ca/challenges#Le%20Lac%20de%20l'Ombre%201%20%F0%9F%90%9F-221)

**Points** : 20

**Category** : Reverse engineering

#### Problem

> Sauriez-vous débarasser les villageois de la menace qui les menace ?
>
> Pechez le premier flag.
> ncat --ssl lacdelombre.chals.hackin.ca 443 

## Solution

We are given a file called `LacDeLOmbre.java`. 

### 1. Analysis

#### 1.1 Flag endpoint 

The `Animal` child class that will show the first flag is `Anguille`, which overrides the method `Animal.apres3Mois()`.

```java
class Anguille extends Animal {
    // ...
	public void apres3Mois() throws PecheException {
		throw new PecheException(Flag.FLAG1);
	}
}
```

#### 1.2 Conditions for input

The implementation of `attrapeLesTous()` 
```java
void attrapeLesTous() throws Exception {
    // ...
    if (appât != null && prise.valeur <= appât.valeur) { /* ... */ }
    if (appât != null && prise.valeur > appât.valeur + 5) { /* ... */ }
    if (appât == null && prise.valeur > 5) { /* ... */}
    // ...
}
```

### 2. Sequence of input

For the third try, we need to assign `prise = Anguille()` somehow.

#### 2.1 Third attempt (idx=2)

For our **third attempt (idx=2)**, enter `serpent géant`. 

```java
class SerpentGéant extends Animal {
    // ...
	public Animal maisEnVrai(int tentative) {
		if (tentative==0) return new Écrevisse().enlarge(3);
		if (tentative==2) return new Anguille().enlarge(6);
		return this;
	}
    // ...
```

However, only choosing `SerpentGeant` on the third attempt means that our `appat == null && prise.valeur == 6`, and will get caught by the last condition :

```java
if (appât == null && prise.valeur > 5) { /* ... */}
```

So we have to assign something before. 

#### 2.2 Second attempt (idx=1)

For the **second attempt (idx=1)**, enter `écrevisse`. This will allow us to bypass the third condition, since `appat == null && prise.valeur == 3`.

```java
class Écrevisse extends Animal {
	Écrevisse() { valeur = 3; }
	public Animal maisEnVrai(int tentative) {
		return this;
	}
}
```

### 2.3 First attempt (idx=0)

For the **first attempt (idx=0)**, we can enter an invalid input.

#### 2.4 Final sequence

```
Que tentez-vous de pécher le premier mois?
> a
Erreur: kaa.ombre.lac.PecheException: Il n'y a pas de ça dans le lac de l'ombre
Et le second mois?
> écrevisse
Et enfin le troisième mois?
> serpent géant
```

This sequence will output the flag :

```
Erreur: kaa.ombre.lac.PecheException: INF600C{ils_se_barrent_en_mission_pendant_trois_mois_et_ils_reviennent_avec_une_anguille}

```

## Flag

```
INF600C{ils_se_barrent_en_mission_pendant_trois_mois_et_ils_reviennent_avec_une_anguille}
```
