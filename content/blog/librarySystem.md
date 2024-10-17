---
date: '2024-10-17T11:10:46-04:00'
draft: false
title: 'LibrarySystem'
---

This work was done in 2021 as an inclass project for a computer science class.

#Name: Colin 
#Collaborators: Gus, Myles, Juan

```py
#Defining Functions for Restrictions

def daysborrowed(book):
    #Reading files to get info
    with open("booklist-2.txt","r") as booklog:
        booklist = []
        booklines = booklog.readlines()
        for line in booklines:
            lines = line.split("#")
            bookname = str(lines[0])
            copies = int(lines[1])
            restriction = str(lines[2])
            booklist.append(bookname)
    with open("librarylog-2.txt","r") as librarylog:
        bookborrowlist = []
        bookreturnlist = []
        dayborrowedlst = []
        dayreturnedlst = []
        loglines = librarylog.readlines()
        for line in loglines:
            lines = line.split("#")
            if lines[0] == "B":
                dayborrowed = int(lines[1])
                personborrowed = str(lines[2])
                bookborrowed = str(lines[3])
                intendedreturnday = int(lines[4])
                bookborrowlist.append(bookborrowed)
                #adding all days said book was borrowed
                if book == bookborrowed:
                    dayborrowedlst.append(dayborrowed)
                
            if lines[0] == "R":
                dayreturned = int(lines[1])
                personreturned = str(lines[2])
                bookreturned = str(lines[3]).strip()
                bookreturnlist.append(bookreturned)
                #adding all days said book was returned
                if book == bookreturned:
                    dayreturnedlst.append(dayreturned)
    print(dayborrowedlst)
    print(dayreturnedlst)
    totaldayslst = []
    length = len(dayreturnedlst) - 1
    i = 0
    totaldays = 0
    lastday = lines[-1]
    while i < length:
#Since each index correlates to eachother in terms of borrow and return, 
#subtract them to get days out then add it to total days
        
        daysout = dayreturnedlst[i] - dayborrowedlst[i]
        totaldays += daysout
        i+=1
        totaldayslst.append(totaldays)
    totaldays = int(totaldays)
    print(totaldays)
    return(totaldays)

#Menu
print("1-Borrow \n2-Return \n3-Pay Fine \n4-Add Book")
userchoice = int(input("Enter the number that correlates to desired function:"))
if userchoice == 1:
    name = str(input("What is your name?: "))
    desiredBook = str(input("What book would you like to borrow?: "))
    currentday = int(input("What is the current day?: "))
    lengthborrowed = int(input("How many days would you like to borrow the book?: "))
elif userchoice == 2:
    name = str(input("What is your name?: "))
    bookreturned = str(input("What book are you returning?: "))
    dayretured = int(input("The day you returned the book: "))
elif userchoice == 3:
    print("PAY FINE")
elif userchoice == 4:
    print("ADD BOOK")
else:
    print("Invalid")
      
    
if desiredBook == bookname:
    if copies == 0:
        print("All copies of",bookname,"are currently borrowed")
if copies > 0:
    copies -=1
    
#Restriction TF Sort
if restriction == "TRUE":
            restrictionlst = []
            restrictionlst.append(bookname)
if restriction == "FALSE":
            nonrestrictionlst.append(bookname)
print("1-Borrow \n2-Return \n3-Pay Fine \n4-Add Book \n5-Stats")
userchoice = int(input("Enter the number that correlates to desired function:"))
if userchoice == 1:
    name = str(input("Please enter your name: "))
    bookname = str(input("What book would you like to borrow: "))
    totaldaysborrowed(bookname)
    borrowcheck(bookname,name)
    if borrowcheck == True:
        print("TRUE")
    if borrowcheck == False:
        print("FALSE")
else:
    print("Invalid")     
currentday = (loglines[-1])
print(currentday)
        

#Restriction Check
if desiredBook == bookname:
    for i in restrictionlst:
        if i == desiredBook:
            bookrestricted = True
    for i in nonrestrictionlst:
        if i == desiredBook:
            bookrestricted = False    
      
        if bookrestricted == True:
            if (dayreturned - daysborrowed) > 7:
                dayfine = dayreturned - daysborrowed
                daysover = dayfine - 7
                fine = daysover*5
if bookrestricted == False:
    if (dayreturned - daysborrowed) > 28:
        dayfine = dayreturned - daysborrowed
        daysover = dayfine
        fine = daysover*1
        
#Check to see if a certain book can be borrowed using the other restriction functions
def borrowcheck(bookname,name):
    if copycheck(bookname) == True:
        if finecheck(name) == False:
            canborrow = True
            return True
        if finecheck(name) == True:
            if payedfine(name) == True:
                return True
        else:
            return False
    else:
        return False

#Checking how many copies a book has when person wants to borrow
def copycheck(bookname):
    with open("booklist-2.txt","r") as booklist:
        booklines = booklist.readlines()
        for line in booklines:
            lines = line.split("#")
            copies = int(lines[1].strip())
        if copies > 0:
            copies -= 1
            return True
        if copies == 0:
            return False
        
#Checking to see if person payed fine
def payedfine(name):
     finedbook = str(input("What book are you paying the fine for?: "))
     with open("librarylog-2.txt","r") as librarylog:
        loglines = librarylog.readlines()
        for line in loglines:
            lines = line.split("#")
            if lines[0] == "P":
                if name == str(lines[2]):
                    if hasfine:
                        daysover = dayreturned- totaldaysintended(bookname)
                        if restrictioncheck(bookname) == True:
                            fineprice = daysover*5
                        if restrictioncheck(bookname) == False:
                            fineprice = daysover*1
                        print("Your fine price is:",fineprice)
                        amountpayed = int(input("How much are you paying?: "))
                        if amountpayed == fineprice:
                            return True
                        else:
                            amountleft = fineprice - amountpayed
                            print("Your fine is now:", amountleft)
                            return False 
                        
#Checking if the person borrowing a book has a pending fine
def finecheck(name):
    with open("librarylog-2.txt","r") as librarylog:
        loglines = librarylog.readlines()
        for line in loglines:
            lines = line.split("#")
            if lines[0] == "R":
                if name == str(lines[2]):
                    dayreturned = int(lines[1])
                    studentname = str(lines[2])
                    book = str(lines[3])
                    if totaldaysborrowed(bookname) < dayreturned:
                        return True
                    else:
                        return False
                                       
#Days Available 
daysAvailable = 0
if boookname(copies) > 1 :
        daysAvailable +=1

for line in loglines:
    popular = max(count , key=count(bookname).get)
print(popular, "Was borrowed for", totaldaysborrowed(bookname) / daysAvailable)                    
```