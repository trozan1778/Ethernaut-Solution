# Ethernaut-Solution

Here I am uploading the solutions to the Ethernaut Challenges to which the link is given below

https://ethernaut.openzeppelin.com/

## 1) **Hello Ethernaut**

This challenge is created to make you familiar with the interface. Here you will learn about the commands that will be helpful while playing the CTF. At this level you will also be setting up the Metamask. Once you are done with all these steps you have to find the passkey that you have to enter to pass this challenge. Also you have to find out the method using which you have to submit the password and if you have followed the ninth step then you must be having it.And still if you do not have the method name then below commands will lead you to the method name.

a) ```await contract.info()```<br/>
b) ```await contract.info1()```<br/>
c) ```await contract.info2("hello")```<br/>
d) ```await contract.infoNum()```<br/>
e) ```await contract.info42()```<br/>
f) ```await contract.theMethodName()```<br/>
g) ```await contract.method7123949()```<br/>


Now to get the passkey below is the solution

First enter the ```contract.abi``` command that helps you interact with the deployed code and come to know about it's functions. When going through the result of the above command you must have find the 'password' method, that will return the passkey to solve the challenge. So enter the ```await contract.password()``` command and you will find the passkey.

Now you have both method name and the passkey. Go and submit the passkey and the challenge will get completed.

## 2) **Fallback**

From the name of the challenge, you can easily interpret that it's related to fallback function.The challenge requires us to claim the ownership of the contract and reduce it's balance to 0.

In this contract we can claim the ownership of the contract using two functions:<br/>
a) contribute<br/>
b) receive()<br/>

In order to claim ownership using contribute function, you will require large amount of ethers.So that's not practically possible and also it's require statement will revert the transaction, even if you send the required ethers.The only way left is using receive() function.This function will get executed on calls to the contract with no data (calldata), e.g. calls made via send() or transfer().
But it also have prerequisite that msg.sender must be having some of there balance in the contract.

For this we will first call contribute function with 0.00001 ether(because amount should be less than 0.001 ether) using javascript console(Press Ctrl+Shift+I in windows)

```contract.contribute({value:"10000000000000"})```

Then we will use send method to trnasfer some ethers to the contract

```contract.send("10000000000000")```

Now you must be the owner of the contract if you have followed the above solution properly.You can check it using the following command

```await contract.owner()```

Now to withdraw the entire balance use withdraw function.

```await contract.withdraw()```

Hurray!!! The Challenge have been completed.
