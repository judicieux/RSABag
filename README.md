# RSABag
Explication du fonctionnement de RSA à l'aide d'un challenge.
## Sommaire
D'abord on va factoriser **N** (modulus) en **p** et **q**. On obtiendra **p** et **q** les deux nombres premiers.<br/>
Ensuite on déterminera **ΦN = (p−1) * (q−1)**.<br/>
Puis on dérivera **d** de **e** et **PHI**.<br/>
Ensuite on déchiffrera le cipher avec **C^d(mod N)**.<br/>
## Challenge
```py
N = 299414541696079057892313324025675176540717914391016340476887410756223595067
e = 65537
c = 4411661085097543909849318376631746681150427615454967763654513675784095561
```
## Solve
On doit factoriser ``N`` en trouvant les nombres premiers (large primes) qui composent la valeur.
Le tool msieve permet de faire ça avec la commande suivante: 
```py
./msieve -q 299414541696079057892313324025675176540717914391016340476887410756223595067

299414541696079057892313324025675176540717914391016340476887410756223595067
p38: 16635750863390806321005018553115635153
p38: 17998258338611020155099256716570289739
```
On a nos deux nombres premiers (large primes):
```py
p = 16635750863390806321005018553115635153, q = 17998258338611020155099256716570289739
```
On calcule le ```PHI (Φ)``` de cette manière:
```py
>>> p = 16635750863390806321005018553115635153
>>> q = 17998258338611020155099256716570289739
>>> print((p - 1) * (q - 1))
299414541696079057892313324025675176506083905189014514000783135486537670176
```
```PHI```:
```py
PHI = 299414541696079057892313324025675176506083905189014514000783135486537670176
```
Maintenant on trouve la clé de déchiffrement.<br/>
**e⁻¹(mod PHI)**:
```py
>>> import libnum
>>> PHI = 299414541696079057892313324025675176506083905189014514000783135486537670176
>>> e = 65537
>>> print(libnum.invmod(e, PHI))
198685307594500543947370403719312567726216991207486644176267723258221107745
```
```Clé de déchiffrement```:
```py
d = 198685307594500543947370403719312567726216991207486644176267723258221107745
```
On a plus qu'à déchiffrer le cipher pour avoir le message.<br/>
**Message = Cipherᵈ(mod N)**:
```py
>>> from Crypto.Util.number import long_to_bytes
>>> d = 198685307594500543947370403719312567726216991207486644176267723258221107745
>>> cipher = 4411661085097543909849318376631746681150427615454967763654513675784095561
>>> N = 299414541696079057892313324025675176540717914391016340476887410756223595067
>>> print(long_to_bytes(pow(cipher, d, N)))
b't0r0nt0_ch4ll{R54_34zY}'
```
## Script
```py
from Crypto.Util.number import bytes_to_long, long_to_bytes, getPrime
from Crypto.Random import get_random_bytes 
import Crypto
import binascii

BITS = 124
flag = "t0r0nt0_ch4ll{R54_34zY}".ljust(30, u'\0')

p = getPrime(BITS, randfunc=get_random_bytes)
q = getPrime(BITS, randfunc=get_random_bytes)

N = p * q
PHI = (p-1) * (q-1)

e = 65537
d = pow(e, -1, PHI)

m = bytes_to_long(flag.encode('utf-8'))
c = pow(m, e, N)
final = pow(c, d, N)

print("p = %s" % p)
print("q = %s" % q)
print("d = %s" % d)
print("e = %s" % e)
print("N = %s" % N)
print("c = %s" % c)
print("flag = %s" % long_to_bytes(final)[:-7])
```
