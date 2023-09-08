Complex Terraform

I agree with all of your reasons, there is no point making the whole thing inversely more complex to save a few lines of code. You just end up spending more time maintaining fewer lines of code whereas on the other hand, more lines of code would have been straightforward to understand and maintain.

I'm seeing the bad practices of people getting too fancy in their tf. I'm from a very strong dev background, and I totally understand how fun it is to be clever, but...

You need to keep the clean code ethos of good programming and apply it to terraform. Clean code means that you understand that the computer can turn anything into functional code, but you know that people need to read and maintain it.

Pragmatic and sensible code screams intent.

My advice, don't get crazy fancy because you can. Smart uses of abstractions, dynamics, maps, sets, count should make code that solves a business problem, but also is humanly reachable. This shit is literally recipes.. don't if chicken soup, then smoke brisket this stuff..
