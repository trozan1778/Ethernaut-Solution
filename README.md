# Ethernaut-Solution

Here I am uploading the solutions to the Ethernaut Challenges to which the link is given below

https://ethernaut.openzeppelin.com/

1) Hello Ethernaut

This challenge is created to make you familiar with the interface. Here you will learn about the commands that will be helpful while playing the CTF. At this level you will also be setting up the Metamask. Once you are done with all these steps you have to find the passkey that you have to enter to pass this challenge. Also you have to find out the method using which you have to submit the password and if you have followed the ninth step then you must be having it.And still if you do not have the method name then below commands will lead you to the method name.

a) await contract.info()
b) await contract.info1()
c) await contract.info2("hello")
d) await contract.infoNum()
e) await contract.info42()
f) await contract.theMethodName()
g) await contract.method7123949()

Now to get the passkey below is the solution
First enter the contract.abi command that helps you interact with the deployed code and come to know about it's functions. When going through the result of the above command you must have find the 'password' method that will return the passkey to solve the challenge. So enter the await contract.password() command and you will find the passkey.

Now you have both method name and the passkey. So now go and submit it and the challenge will be completed.

