# Week 5 - Ghidra




This week we learned how to use the tool Ghidra to solve some crackmes by deassembling and decompiling executables.




---

# Crackme 1: http://crackmes.cf/users/seveb/crackme05/download/crackme05.tar.gz




To solve this crackme, you need to generate a key that follows the rules contained in the functions rock, paper, scissors, and cracker.

My solution is the code below written in python which generates all possible keys. For example 01z0-0000-0240-02z0 is a valid key.

`
valid_chars = [ _ for _ in range(48, 58)]
valid_chars.extend([_ for _ in range(65, 91)])
valid_chars.extend([_ for _ in range(97, 123)])
valid_chars.append(45)


c14 = 45
c4 = 45
c9 = 45
for c1 in valid_chars:
    for c2 in valid_chars:
        if (c1 + c2) < 171:
            continue
        for c8 in valid_chars:
            for c10 in valid_chars:
                c3 = (c10 ^ c8) + 48
                c5 = c3
                if c3 >= 58:
                    continue
                for c13 in valid_chars:
                    c0 = (c13 ^ c5) + 48
                    c18 = c0
                    if c0 >= 58:
                        continue
                    for c16 in valid_chars:
                        for c17 in valid_chars:
                            if c16 + c17 < 171:
                                continue
                            if c1 + c2 == c16 + c17:
                                continue
                            for c15 in valid_chars:
                                for c6 in valid_chars:
                                    for c7 in valid_chars:
                                        for c11 in valid_chars:
                                            for c12 in valid_chars:
                                                print(str(chr(c0)) + str(chr(c1)) + str(chr(c2)) + str(chr(c3)) + str(chr(c4)) + str(chr(c5)) + str(chr(c6)) + str(chr(c7)) + str(chr(c8)) + str(chr(c9)) + str(chr(c10)) + str(chr(c11)) + str(chr(c12)) + str(chr(c13)) + str(chr(c14)) + str(chr(c15)) + str(chr(c16)) + str(chr(c17)) + str(chr(c18)))

`

How I did it using Ghidra:

I opened the crackme in Ghidra. I found the `main` function and noticed four interesting function calls. I started with the first one called `rock` inside of which I found the `bomb` function being called a few times. Looking at that `bomb` function I saw that it printed a lose message and stopped the program with error. So I knew I wanted to avoid reaching that. In order to do that I had to avoid the if statements in `rock` that lead me to `bomb`. Looking in `paper`,  `scissors`, and `cracker` I saw the same thing, if statments with calls to `bomb`. I then took these if statements and generated rules that would allow me to avoid the `bomb` function. These rules are as follows:


key[14] + key[4] + key[9] = 135
key[1] + key[2] >= 171
key[16] + key[17] >= 171
key[1] + key[2] != key[16] + key[17]
key[3] = key[5] = (key[10] ^ key[8]) + 48
key[0] = key[18] = (key[13] ^ key[5]) + 48


After finding these rules I was able to run it and pass the key as an argument to get the success message.



---

# Crackme 2 http://crackmes.cf/users/adamziaja/crackme1/download/crackme1.tar.gz




To solve this crackme, you need to generate a key based on the username (8-12 characters) entered. The key consists of 8 characters of the ASCII values for each character in the username with alterrnating upper and lowercase for any letters. Additionally the key is shifted by (2 * username_len) - 8.

My solution is the code below written in python which generates a key based on the entered username.

`
uname = input("Enter a username between 8 and 12 characters: ")
serial = ""

if len(uname) < 8 or len(uname) > 12:
    print("Invalid username")
    exit(1)

odd = True
for c in uname:
    if c.isalpha():
        if odd:
            serial += str(ord(c.lower()))
            odd = False
        else:
            serial += str(ord(c.upper()))
            odd = True
    else:
        serial += str(ord(c))

shift_index = (len(uname) - 8) * 2
print(serial[shift_index:shift_index + 8])
`

How I did it using Ghidra, ltrace, and gdb:

I opened it first in Ghidra and looked at the `main` function. I found where it reads in the username and serial number (the key).  I then saw an if statement where it checks if the username is between 8 and 12 characters inclusive giving me a constraint on the username. I then saw a loop over some string where it is setting odd characters to lowercase and evens to uppercase. I continued to explorre the main function but couldn't make sense of much else except that we wanted the calculated serial to be equal to the entered serial to get the OK message. Since I was stuck I decided to use some of the other tools to figure out what the other loop is doing. First, I tried ltrace which confirmed my theory about the odd alphabetic characters becoming uppercase and even becoming lowercase. Nothing happened to other characters. I then moved on to gdb to figure out the other loop. I ran through it using abcdefgh as the username and checked the local variables using `info locals` at every interesting step. I found a series of numbers being written to `serialstring`. Then I found the first 8 characters of that string being copied to `serialstring2` in the second for loop. I tried a few different inputs like abcdefghi, aaaaaaaa, and 12345678 to see if  my hypothesis was correct. Through this I also found that any username above length 8 shifts which characters are copied by 2 * (len  - 8). The username aaaaaaaa gave me a lot of insight into how the numbers were generated since its resulting serial was 97659765 which is ASCII aA repeating. I again tested this theory with several alphabetic, numerical, and other characters which confirmed the key is generated using the ASCII values of the transformed and shifted username.




---

# Crackme 5 http://crackmes.cf/users/seveb/crackme04/download/crackme04.tar.gz




To solve this crackme, you need to generate a key that .

My solution is the code below written in python which generates .

`

`

How I did it using Ghidra:



