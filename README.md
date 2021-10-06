# RSA

import random       #random import is used to get access to the random module.

def LargePrime(key):        #def is used to define a function, using LargePrime as a function.
  while True:       #while loop will run as long as the conditional expression evaluates to True.
    num = random.randrange(2**(key-1), 2 ** key)        #function for integers.
    if isPrime(num) and num % 2 == 1:       #is prime or not.
      return num

def MR (num):
  s = num - 1       # -subtraction; subtracts right hand operand from left hand operand.
  t = 0
  while s % 2 == 0:
    s = s // 2      # //; floor division.
    t += 1
  for tryouts in range(40):
    a = random.randrange(2, num - 1)
    v = pow(a, s, num)
    if v != 1:      # != is used when the values of two operands are not equal.
      i = 0
      while v != (num - 1):
        if i == t - 1:
          return False
        else:
          i = i + 1                 # +addition; adds values on either side of the operator.
          v = (v ** 2) % num        # **exponent; performs exponential (power) calculation on operators.
  return True

def isPrime(num):

  if (num < 2):             # < is used when the value of left operand is less than the value of right operand.
    return False

  SmallPrimes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103, 107, 109, 113, 127, 131, 137, 139, 149, 151, 157, 163, 167, 173, 179, 181, 191, 193, 197, 199, 211, 223, 227, 229, 233, 239, 241, 251, 257, 263, 269, 271, 277, 281, 283, 293, 307, 311, 313, 317, 331, 337, 347, 349, 353, 359, 367, 373, 379, 383, 389, 397, 401, 409, 419, 421, 431, 433, 439, 443, 449, 457, 461, 463, 467, 479, 487, 491, 499, 503, 509, 521, 523, 541, 547, 557, 563, 569, 571, 577, 587, 593, 599, 601, 607, 613, 617, 619, 631, 641, 643, 647, 653, 659, 661, 673, 677, 683, 691, 701, 709, 719, 727, 733, 739, 743, 751, 757, 761, 769, 773, 787, 797, 809, 811, 821, 823, 827, 829, 839, 853, 857, 859, 863, 877, 881, 883, 887, 907, 911, 919, 929, 937, 941, 947, 953, 967, 971, 977, 983, 991, 997]
                    #pre generated prime values.
  if num in SmallPrimes:
    return True

  for prime in SmallPrimes:
    if (num % prime == 0):
      return False
  return MR (num)


def MRT(s=512):     #using miller-rabin primality test to choose prime number with 512 bits.
  return LargePrime(s)

def EA(e,r):    #using euclidean algorithm to evaluate gcd.
  while(r!=0):
    e,r = r, e % r
  return e


def powmod_sm(x, y, p):         #square & multiply algorithm.
	res = 1
	x = x % p      # %modulus; divides left hand operand by right hand operand and returns remainder.
	if (x == 0):
		return 0
	while (y > 0):     # > is used when the value of left operand is greater than the value of right operand, then condition becomes true.
		if ((y & 1) == 1):
		  res = (res * x) % p

		y = y >> 1	 # y = y/2
		x = (x * x) % p
	return res

def EEA(a,b):   #using extended euclidean algorithm to find modular inverse of the value.
  if(a % b == 0):
    return(b,0,1)
  else:
    gcd,s,t = EEA(b,a%b)
    s = s-((a//b) * t) 	# *multiplication; multiplies values on either side of the operator.
  return(gcd,t,s)

def modInverse(e,r):
  gcd,s,_= EEA(e,r)
  if(gcd!=1):
    return None
  else:
    return s % r


def keygen():
  p = MRT()
  q = MRT()
  if p==q:      # == is used when the values of two operands are equal.
    q = MRT()

  n = p*q       # *multiplication; multiplies values on either side of the operator.
  print("\n\nRSA Modulus(n) is:",n)

  t = (p-1)*(q-1)
  print("\n\nEulers Toitent(r) is:",t)
  '''FINDS THE HIGHEST POSSIBLE VALUE OF 'e' BETWEEN 1 and 1000 THAT MAKES (e,r) COPRIME.'''
  for i in range(1,1000):
    if(EA(i,t)==1):
      e = i
  print("\n\nThe value of e is:",e)
  d = modInverse(e,t)
  run = {}
  run['public_key']=(e,n)
  run['private_key']=(d,n)

  return run

def encrypt(pub_key,n_text):
  e,n=pub_key
  x =[]
  cipher = ""
  m = 0
  for i in n_text:
    m = ord(i)
    c = powmod_sm(m,e,n)
    x.append(c)
    cipher+=str(c)      #str() function is used to return the string version of the object.
    cipher+=','         # += is used to add right operand to the left operand and assign the result to left operand.
  return cipher[:-1]

def decrypt(priv_key,c_text):
  d,n=priv_key
  txt=list(map(int,c_text.split(',')))
  x = ''
  m = 0

  for i in txt:
    m = powmod_sm(i,d,n)
    c = chr(m)          #chr() takes a single integer.
    x+= c
  return x


def main():
  message = input("Enter the text to encrypt:\n")
  keys = keygen()
  public_key = keys['public_key']
  private_key = keys['private_key']
  encrypted = encrypt(public_key,message)

  print("\n\nencrypted: ",encrypted)

  decrypted = decrypt(private_key,encrypted)
  print("\n\ndecrypted: ",decrypted)
main()
