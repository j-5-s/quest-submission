## Chapter 4, Day 1

1. Explain what lives inside of an account. 

Contract code and storage of data lives inside an account. This is different than ethereum where a contract has a reference to an account.

2. What is the difference between the `/storage/`, `/public/`, and `/private/` paths?

Data lives in `/storage/` while `/public/` and `/private/` are scopes that specify access
to that data.  Private will allow only certain accounts to access, while public will allow anyone the ability to read/write to.

3. What does `.save()` do? What does `.load()` do? What does `.borrow()` do?

`.save()` is the function to write to storage (moving something into storage), `load()` is a way to moving something out of storage.  `.borrow()` gives the account a reference to the storage object so that it can read data (without moving it) such as using metadata to display an NFT.

4. Explain why we couldn't save something to our account storage inside of a script.
To store something you need both write access and access to the AuthAccount.  Scripts contain neither.

5. Explain why I couldn't save something to your account.
Only the signers of the transaction are available to save something.

6. Define a contract that returns a resource that has at least 1 field in it. Then, write 2 transactions:

    1) A transaction that first saves the resource to account storage, then loads it out of account storage, logs a field inside the resource, and destroys it.

    https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=tx&id=d8781e1f-f158-4981-98f3-ed1330630f79&storage=none

    2) A transaction that first saves the resource to account storage, then borrows a reference to it, and logs a field inside the resource.

    https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=tx&id=8840b1cf-067c-4d94-8f80-bd9eb7535494&storage=none