# Week 6 - Ghidra and uftrace




This week we learned how to use uftrace to see what is happening when a program is executed. We also practiced more with Ghidra to solve some crackmes by deassembling and decompiling executables.




---

# Ezcrackme 1:




The password for this crackme is `picklecucumberl337`

How I did it using uftrace:

I ran uftrace on the crackme and entered a random password. I then saw this random password being compared with `picklecucumberl337` before the faliure message prints. This tells me `picklecucumberl337` is probably the password. When I test this hypothesis and enter `picklecucumberl337` as the password I get a sucess message.



---

# Ezcrackme 2:




The password for this crackme is `artificialtree`

How I did it using uftrace:

Similar to the last one, I ran uftrace on the crackme and entered a random password. I again saw this random password being compared with a string, this time `artificialtree`, before the faliure message prints. This tells me `artificialtree` is probably the password. When I test this hypothesis and enter `artificialtree` as the password I get a sucess message.



---

# Ezcrackme 3:




The password for this crackme is `strawberrykiwi`

How I did it using uftrace:

Similar to the last two, I ran uftrace on the crackme and entered a random password. I this time I saw `strrawberry` and `kiwi`  being concatenated and this random password being compared with the result, `strawberrykiwi`, before the faliure message prints. This tells me `strawberrykiwi` is probably the password. When I test this hypothesis and enter `strawberrykiwi` as the password I get a sucess message.



---

# Control Flow 1:




To solve this crackme, you need to generate a key based on various rules found in various functions that lead to the success message.

My solution is the code below written in python which generates a random key that fulfills the rules. The length is limited to 16 characters for simplicity, however, longer keys will work.

```
import string
import random

password = []

letters_and_numbers = string.ascii_letters + string.digits
for i in range(16):
    password.append(random.choice(letters_and_numbers))
password[0] = 'A'
password[1] = '6'
password[3] = '2'
password[7] = '%'
password[15] = '*'
print("".join(password))
```

How I did it using Ghidra:

I opened the crackme in Ghidra. I found the `main` function and noticed some if statements and an interesting function call. The if statements showed that if the length of the input was less than 16 a faliure message would be printed. I then looked at the `rock` function where I found a call to `paper` along with some other faliure messages. So I knew I wanted to go to the paper funtion next. In order to do that I had to avoid the if statements in `rock` that lead me to faliure messages and to go inside the if statements that lead me to `paper`. Following this path I found that `paper` called `scissors` which called `lizard` which called `spock` which called `win` which printed a success message. I then took the if statements that lead down this path and generated rules that would allow me to get to the `win` function. These rules are as follows:


password[0] = 'A'

password[1] = '6'

password[3] = '2'

password[7] = '%'

password[15] = '*'


After finding these rules I was able to create a keygen, run it, and pass the key as an argument to get the success message.


---

# Control Flow 2:




To solve this crackme, you need to generate a key based on various rules found in various functions that lead to the success message.

My solution is the code below written in python which generates a random key that fulfills the rules. The length is limited to 16 characters for simplicity, however, longer keys will work.

```
import string
import random

password = []

letters_and_numbers = string.ascii_letters + string.digits
for i in range(16):
    password.append(random.choice(letters_and_numbers))
password[6] = 'Y'
password[8] = '#'
password[10] = 'A'
password[11] = '*'
password[13] = '6'
print("".join(password))
```

How I did it using Ghidra:

I opened the crackme in Ghidra. I found the `main` function and noticed some if statements and an interesting function call. The if statements showed that if the length of the input was less than 16 a faliure message would be printed. I then looked at the `rock` function where I found a call to `paper` along with some other faliure messages. So same as last one I knew I wanted to go to the paper funtion next and so I had to avoid the if statements in `rock` that lead me to faliure messages and to go inside the if statements that lead me to `paper`. Again I found a path from `paper` to `scissors` to `lizard` to `spock` to `win`. I again took the if statements that lead down this path and generated rules that would allow me to get to the `win` function. These rules are as follows:


password[6] = 'Y'

password[8] = '#'

password[10] = 'A'

password[11] = '*'

password[13] = '6'


After finding these rules I was able to create a keygen, run it, and pass the key as an argument to get the success message.


---

# Control Flow 3:




To solve this crackme, you need to generate a key based on various rules found in various functions that lead to the success message.

My solution is the code below written in python which generates a random key that fulfills the rules.

```
import string
import random

password = []

letters_and_numbers = string.ascii_letters + string.digits
for i in range(16):
    password.append(random.choice(letters_and_numbers))
password[6] = chr(ord(password[1]) + ord(password[3]) - ord(password[5]))
while(ord(password[6]) ^ ord(password[7]) >= 3):
    password[7] = random.choice(letters_and_numbers)
password[10] = password[12]
while(ord(password[8]) ^ ord(password[7]) < 4):
    password[8] = random.choice(letters_and_numbers)
while(ord(password[12]) ^ ord(password[8]) ^ ord(password[9]) == 0):
    password[9] = random.choice(letters_and_numbers)
print("".join(password))
```

How I did it using Ghidra:

I opened the crackme in Ghidra. I found the `main` function and noticed some if statements and an interesting function call. The if statements showed that if the length of the input was less than or greater than 16 a faliure message would be printed. I then looked at the `rock` function where I found a call to `paper` along with some other faliure messages. So same as last two I knew I wanted to go to the paper funtion next and so I had to avoid the if statements in `rock` that lead me to faliure messages and to go inside the if statements that lead me to `paper`. Again I found a path from `paper` to `scissors` to `lizard` to `spock` to `win`. I again took the if statements that lead down this path and generated rules that would allow me to get to the `win` function. These rules are as follows:


password[6] = password[1] + password[3] - password[5]

password[6] ^ password[7] < 3

password[10] = password[12]

password[8] ^ password[7] >= 4

password[12] ^ password[8] ^ password[9] != 0


After finding these rules I was able to create a keygen, run it, and pass the key as an argument to get the success message.

