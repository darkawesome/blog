---
date: '2024-10-17T11:10:02-04:00'
draft: false
title: 'Anagram'
---
# Python work from intro class

The following was an optional assignment done in 2021 for a comp sci class

```py
string1 = input("Enter a string: ")
string2 = input("Enter a string: ")

string1 = string1.lower()
string2 = string2.lower()

string1_letters = []
string2_letters = []

for i in range(len(string1)):
    
  if ord(string1[i]) >= 48 and ord(string1[i]) <= 57:
      
    string1_letters.append(string1[i])
  elif ord(string1[i]) >= 97 and ord(string1[i]) <= 122:
      
    string1_letters.append(string1[i])
for i in range(len(string2)):
    
  if ord(string2[i]) >= 48 and ord(string2[i]) <= 57:
      
    string2_letters.append(string2[i])
    
  elif ord(string2[i]) >= 97 and ord(string2[i]) <= 122:
      
    string2_letters.append(string2[i])

letters1 = sorted(string1_letters)
letters2 = sorted(string2_letters)

if letters1 == letters2:
  print("They are anagrams")
else:
  print("They not anagrams")
```