# Javascript casino 2

## Introduction

### Description
```
Alors que vous passez devant la salle fumeur, vous voyez un papier brûler dans le cendrier. 
Par instinct, vous éteignez le feu et vous vous rendez compte qu'il y a du code sur ce papier! Du code...

code_ptsd

Vous devriez analyser ça.

https://jffi24-jscasino2.chals.hackin.ca
```

`Link` : [Javascript Casino 2](https://ctf.hackin.ca/challenges#Javascript%20casino%202%20%F0%9F%8E%B0%F0%9F%8E%B0-144)
`Points` : 100
`Category` : Web 

## 1. Analysis

### 1.1 Javascript equality

The javascript source code is provided.

This time, there are new rig functions and a new check function :

```js
// Personne ne pourra obtenir le même chiffre, et une String ne devrait pas être égale à un nombre :D
function jackpot_check(vals) {
    return String(vals.join('')) == 110930363110120583499530411000420232389378489;
}
```

Not being familiar with javascript, I try to check the truth values of different comparisons with console.log.

Comparing the number with the String representation of itself outputs `true`.
Here are some other useful comparisons that help understand whats going on :

```js
// True (same number)
"110930363110120583499530411000420232389378489" == 110930363110120583499530411000420232389378489;

// True (modify last digit)
"110930363110120583499530411000420232389378480" == 110930363110120583499530411000420232389378489;

// False (remove last digit)
"11093036311012058349953041100042023238937848" == 110930363110120583499530411000420232389378489;

// False (modify first digit)
"010930363110120583499530411000420232389378489" == 110930363110120583499530411000420232389378489;
```

This must mean that the string must be the same length as the integer.
Most importantly, it must mean that there is a range after which modifying the digits doesn't affect the equality.

### 1.2 Finding the range 

To find the range where modifying the digits doesnt affect the equality, 
i try to change the digits until i find the last position where modifications affect it.

I find that the range where the digits must be equal to the integer (in-between the brackets)

```
[1109303631101205834995304]11000420232389378489 
```

This means that all we have to do is ensure that this section is the same in our input.





## 2. Solution

## 2.1 Bypassing the rigged input

In the source code, the user's input goes through the following functions :

```js
function get_values(fresh_nums) {
    if (fresh_nums.length > 0) {
        fresh_nums = rig_it(fresh_nums);
        fresh_nums = rig_it_again(fresh_nums);
				console.log();
    }
    return fresh_nums;
}

// On multiplie selon l'envie de la personne qui programme.
function rig_it(vals) {
    vals[0] *= 110;
    vals[1] *= 11;
    vals[2] *= 10;
    vals[3] *= rand_int(100,222);
    vals[4] *= rand_int(1000, 5555);
    vals[5] *= 1;
    return vals;
}

// On ajoute des chiffres pour que personne n'obtienne le jackpot
function rig_it_again(vals) {
    vals[0] += '930';
    vals[1] += '110';
    vals[2] += '583';
    vals[3] += String(rand_int(100,999));
    vals[4] += String(rand_int(100,999));
    vals[5] += String(rand_int(100,999));
    return vals;
}
```

The only part that matters here is `vals[0] *= 110`.
In order to get the correct number in the first position of the String, it must be a number that, when multiplied by 110, equals what we're looking for.

So, the input in the first position of our array will be :

```
i = 1109303631101205834995304 / 110 = 100845784645564180
```

The rest of the elements in the array will be 0, until the length is adequate. 
So, an array with a valid values :

```
[100845784645564180,0,0,0,0,0,0,0]
```

## 3. Flag

```
JFFI{C'est_encore_moi_le_JS.1=="1"_jusqu'à_un_certain_nombre_maximal}
```
