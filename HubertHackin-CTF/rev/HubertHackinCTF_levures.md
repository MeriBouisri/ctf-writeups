# Levures

## Introduction

**Link** : [levures](https://ctf.hackin.ca/challenges#levures-183)

**Points** : 200

**Category** : Reverse engineering

#### Problem

> Vous en voulez combien des levures?

## Solution

We are given a file called `a`, with no extension. 

### 1. Analysis

#### 1.1 Running the file

First start by running the file as an executable

```bash
$ ./a
```

The program will print "Début de la période de fermentation...", and will keep running without doing anything.
Interrupting the program after a while will print the following to the console :

```
Début de la période de fermentation...
^CTraceback (most recent call last):
  File "./a.py", line 18, in <module>
    print('JFFI{' + str(a(4, 2))[32:64] + '}')
  File "./a.py", line 13, in main

KeyboardInterrupt
```

So, we know that we are dealing with a python executable `a.py`

### 2. Reverse engineering the executable (Optional)

This part is optional and serves as analysis. Skip to the `3. Ackermann Function` for the solution.

#### 2.1 Unpacking python executables

In order to extract the source code from the executable, I used the following resource which explains how to do so : 

- [Unpacking Python Executables on Windows and Linux](https://www.fortinet.com/blog/threat-research/unpacking-python-executables-windows-linux)

Following the **Unpacking python >= 3.9 on Linux** section, I installed the tool [Decompyle++](https://github.com/zrax/pycdc) :

```bash
git clone https://github.com/zrax/pycdc
cd pycdc/
mkdir build
cd build/
cmake ..
make
```

And then used the program to unpack the python source code from the executable to a new file `a.py`:

```bash
pycdc a > a.py
```

Which outputs the following :

```python
# Source Generated with Decompyle++
# File: a (Python 3.10)

import time

def a(m, n):
    if m == 0:
        return n + 1
    if None == 0:
        return a(m - 1, 1)
    return None(m - 1, a(m, n - 1))


def main():
    print('Début de la période de fermentation...')
    time.sleep(18000)
    print('Il faut compter les levures puis en sélectionner le bon montant.')
    print('JFFI{' + str(a(4, 2))[32:64] + '}')

if __name__ == '__main__':
    main()
    return None
```

We can see that the program was stuck earlier on `time.sleep(18000)`, so we can remove that.
However, the program is still not valid. 

### 2.2 Fixing code by dynamic analysis 

The decompiled source code has failed in the function `a(m, n)`. As explained in the article linked earlier,
we will need to use dynamic analysis to fix the program.

```bash
pycdas a > a_asm
```

Using this part, we can fix the function :

```
[Code]
    File Name: ./a.py
    Object Name: a
    Arg Count: 2
    Pos Only Arg Count: 0
    KW Only Arg Count: 0
    Locals: 2
    Stack Size: 6
    Flags: 0x00000043 (CO_OPTIMIZED | CO_NEWLOCALS | CO_NOFREE)
    [Names]
        'a'
    [Var Names]
        'm'
        'n'
    [Free Vars]
    [Cell Vars]
    [Constants]
        None
        0
        1
    [Disassembly]
        0       LOAD_FAST                       0: m
        2       LOAD_CONST                      1: 0
        4       COMPARE_OP                      2 (==)
        6       POP_JUMP_IF_FALSE               8 (to 16)
        8       LOAD_FAST                       1: n
        10      LOAD_CONST                      2: 1
        12      BINARY_ADD                      
        14      RETURN_VALUE                    
        16      LOAD_FAST                       1: n
        18      LOAD_CONST                      1: 0
        20      COMPARE_OP                      2 (==)
        22      POP_JUMP_IF_FALSE               19 (to 38)
        24      LOAD_GLOBAL                     0: a
        26      LOAD_FAST                       0: m
        28      LOAD_CONST                      2: 1
        30      BINARY_SUBTRACT                 
        32      LOAD_CONST                      2: 1
        34      CALL_FUNCTION                   2
        36      RETURN_VALUE                    
        38      LOAD_GLOBAL                     0: a
        40      LOAD_FAST                       0: m
        42      LOAD_CONST                      2: 1
        44      BINARY_SUBTRACT                 
        46      LOAD_GLOBAL                     0: a
        48      LOAD_FAST                       0: m
        50      LOAD_FAST                       1: n
        52      LOAD_CONST                      2: 1
        54      BINARY_SUBTRACT                 
        56      CALL_FUNCTION                   2
        58      CALL_FUNCTION                   2
        60      RETURN_VALUE                    
```

Following this, we can rewrite the function like so :

```python
# Source Generated with Decompyle++
# File: a (Python 3.10)

import time

def a(m, n):
    if m == 0:
        return n + 1
    if n == 0:
        return a(m - 1, 1)
    return a(m - 1, a(m, n - 1))

def main():
    print('Début de la période de fermentation...')
    print('Il faut compter les levures puis en sélectionner le bon montant.')
    print('JFFI{' + str(a(4, 2))[32:64] + '}')

if __name__ == '__main__':
    main()
```
However, running the file again will result in a `RecursionError`.

### 3. Ackermann Function

#### 3.1 Output for (4, 2)

While trying to figure out what the function is, I finally found out that the implementation is known as the [Ackermann Function](https://mathworld.wolfram.com/AckermannFunction.html)
However, the input values `a(4, 2)` are far too demanding for a regular computer, resulting in a `RecursionError`.

Thankfully, `WorlframAlpha` provides the answer to the input `a(4,2)`

- [Ackermann function of 4, 2](https://www.wolframalpha.com/input/?i=Ackermann+function+%284%2C2%29)

Which gives us the following number :

```
2.00352993040684646497907235156025575044782547556975141926501697371089405955631145308950613088093334810103823434290726318... × 10^19728
```

#### 3.2 Extracting digits

In the `main()` function, the digits we're looking for are from index 32 to 64

```python
def main():
    print('Début de la période de fermentation...')
    print('Il faut compter les levures puis en sélectionner le bon montant.')
    print('JFFI{' + str(a(4, 2))[32:64] + '}')
```

We can copy/paste the value and use it as a string to extract the characters at the right positions (Don't forget the remove the ".", since we need the digits and not the decimal representation):

```python
ack = "20035299304068464649790723515602557504478254755697514192650169737108940595563114530895061308809333481010382"
print(ack[32:64])
```

Which will output the following string :

```
55750447825475569751419265016973
```

## Flag

```
JFFI{55750447825475569751419265016973}
```
