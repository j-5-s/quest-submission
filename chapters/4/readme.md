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

    1) A transaction that first saves the resource to account storage, then borrows a reference to it, and logs a field inside the resource.

    https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=tx&id=8840b1cf-067c-4d94-8f80-bd9eb7535494&storage=none


## Chapter 4, Day 2

Please answer in the language of your choice.

1. What does `.link()` do?

      Exposes your resources publicly (or privately) through a capability.

2. In your own words (no code), explain how we can use resource interfaces to only expose certain things to the `/public/` path.

    By linking a interface publically you can restrict users access to other methods defined in the implementation of the class itself, limiting the access to just that interface.

3. Deploy a contract that contains a resource that implements a resource interface. Then, do the following:

    [Contract](https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=account&id=c96528eb-b154-45aa-b46a-8ae5ca45e815&storage=none)
   

    1) In a transaction, save the resource to storage and link it to the public with the restrictive interface. 
        [Transaction](https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=tx&id=d8749e41-c445-47f4-a43f-acaa936165d3&storage=none)
        ```cadence
          import HelloWorld from 0x01

          transaction {

            prepare(acct: AuthAccount) {
              let latitude = "33.7490"
              let longitude = "84.3880"
              let destination <- HelloWorld.createDesitinationMarker(latitude: latitude, longitude: longitude, name:"atlanta");
              acct.save(<- destination, to: /storage/MyDestination)

              acct.link<&HelloWorld.Destination{HelloWorld.City}>(/public/MyCity, target: /storage/MyDestination)
            }

            execute {
              log("done")
            }
          }


        ```

    2) Run a script that tries to access a non-exposed field in the resource interface, and see the error pop up.
        [script](https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=script&id=1b7b7a00-cf64-45bd-8b86-38f137d26ce9&storage=none)
        ```cadence
        import HelloWorld from 0x01

        pub fun main(account: Address) {
          let capa: Capability<&HelloWorld.Destination{HelloWorld.ILatLong}> = getAccount(account).getCapability<&HelloWorld.Destination{HelloWorld.ILatLong}>(/public/MyLatLng) 
          let publicLatLng = capa.borrow() ?? panic("could not get")

          log(publicLatLng.latitude)
        }
        ```

    3) Run the script and access something you CAN read from. Return it from the script.
        [script](https://play.onflow.org/18e3feb7-a271-4134-8886-0b05652ac46c?type=script&id=89ca1eca-f75e-43ab-9269-f5fa17381070&storage=none)
        ```cadence
        import HelloWorld from 0x01

        pub fun main(account: Address) {
          let capa: Capability<&HelloWorld.Destination{HelloWorld.ICity}> = getAccount(account).getCapability<&HelloWorld.Destination{HelloWorld.ICity}>(/public/MyCity) 
          let publicCity = capa.borrow() ?? panic("could not get cability")

          log(publicCity.name)
        }
        ```

## Chapter 4, Day 3


1. Why did we add a Collection to this contract? List the two main reasons.

    A collection gives you the ability to store many objects in one storage path.

2. What do you have to do if you have resources "nested" inside of another resource? ("Nested resources")

    create a destroy function so that the destroy of the parent cascades to the children

3. Brainstorm some extra things we may want to add to this contract. Think about what might be problematic with this contract and how we could fix it.

    - Idea #1: Do we really want everyone to be able to mint an NFT? 🤔. 
    - The ability to transfer an NFT to another account
    - The ability to sell an NFT

    - Idea #2: If we want to read information about our NFTs inside our Collection, right now we have to take it out of the Collection to do so. Is this good?
