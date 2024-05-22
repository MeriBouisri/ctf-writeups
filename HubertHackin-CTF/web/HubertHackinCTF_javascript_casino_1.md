# Javascript casino 1 

## Introduction

**Link** : [Javascript Casino 1](https://ctf.hackin.ca/challenges#Javascript%20casino%201%20%F0%9F%8E%B0-143)

**Points** : 100

**Category** : Web 

#### Problem

> Vous partez vous chercher une boisson au bar et vous engagez dans une discussion avec le barman... Il vous dit qu'il a trouvé comment hacker la machine à sous. Il vous demande votre courriel et vous recevez le fichier ci-dessous dans votre boite de réception.
> Au travail !
>
> https://jffi24-jscasino.chals.hackin.ca

## Solution

### 1. Analysis

The javascript source code is provided.

Here is the function that checks a valid input :

```js
// Personne ne devrait gagner avec cette fonction !
function jackpot_check(vals) {
    vals.sort();
    return (is_sorted(vals) == true && vals.length > 7 && has_no_duplicates(vals))
}
```

I use console.logs to check the output of each operation, since I am not familiar with javascript.
I notice the function`vars.sort()` sorts the elements alphabetically, but the`is_sorted(vals)` methode 
checks if the elements have been sorted as numbers :

```js
function is_sorted(vals) {
    for (let i = 0; i < vals.length - 1; i++) {
        if (vals[i] > vals[i + 1]) {
            return false;
        }
    }
    return true;
}
```

### 2. Constructing the array

Here are the functions that modify our input before checking it :

```js
function get_values(fresh_nums) {
    if (fresh_nums.length > 0) {
        fresh_nums = rig_it(fresh_nums);
        fresh_nums = rig_it_again(fresh_nums);
    }
    return fresh_nums;
}

// On ajoute des chiffres pour que personne n'obtienne le jackpot
function rig_it(vals) {
    vals.push(11);
    vals.push(1);
    return vals;
}

// On multiplie selon l'envie de la personne qui programme
function rig_it_again(vals) {
    vals[0] *= 110
    vals[1] *= 11
    vals[2] *= 10
    vals[3] *= 111
    vals[4] *= 1000
    vals[5] *= 1
    return vals;
}
```

Since the `sort()` method sorts alphabetically, we must construct
our array according to the `rig_it_again(vals)` function. 
The positions that get multiplied with the highest number must begin with the highest digit as well.

Since the `rig_it(vals)` function adds the values `[1, 11]`, we must add 6 values to our array
so that the added values arent affected by the operations in `rig_it_again(vals)`.

Here's an array with a valid values :

```
[5,4,3,6,7,800000]
```

## Flag

```
JFFI{Je_suis_du_JavaScript.Je_trie_des_chiffres_comme_des_lettres}
```
