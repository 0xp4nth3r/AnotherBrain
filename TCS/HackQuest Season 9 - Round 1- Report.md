

### Codepool

WF9{tktgnadfjprxdjhstpseddawphpetitg}                                                                                                                                                                                                                                              
Decode it using ROT7

![[Pasted image 20250125161308.png]]

### Mystic Grimoire


~~~
strings  QuestFile_MysticGrimoire.png
~~~

![[Pasted image 20250125161531.png]]

### Dussahas Dice


using Dev tool to get six in both dice

~~~
Math.random = () => 0.999; 
delete Math.random;
checkRolls(6, 6);
~~~

![[Pasted image 20250125161921.png]]


### The Mask

Unlock the Pdf with password `password`

![[Pasted image 20250125162150.png]]

Copy the image containing HQ9 key

![[Pasted image 20250125162227.png]]

We can able to get the flag

### A Bug's Life

Use the Dev tool 

~~~
gameRunning = true; 
enableCatchingFeature = true;    
handleLadybugClick();
~~~

![[Pasted image 20250125162534.png]]

### Breaking Bad

Welcome to the "Breaking Bad" challenge! In this cryptographic puzzle, your mission is to decipher a secret message that has been cleverly encoded. The theme revolves around the idea of breaking down this chemical compound and uncovering hidden truths.

~~~
HQ9{1 89 19 50 39 103 5 9 74 16 59 6 19 114 23 1 1 5 83 15 23 33 6 19}         
~~~

Decode it With This Table


![[Pasted image 20250125162924.png]]

and Change all To Caps 

