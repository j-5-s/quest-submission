## Chapter 3, Day 1

As always, feel free to answer in the language of your choice.

1. In words, list 3 reasons why structs are different from resources.
  - They can not be copied.
  - They cant be lost or overwritten.
  - They cant be created whenever you want.
2. Describe a situation where a resource might be better to use than a struct.
  When you want to handle something of value.  Using a resource will
  protect it from unintentionally being lost or copied.

3. What is the keyword to make a new resource?
  `create`

4. Can a resource be created in a script or transaction (assuming there isn't a public function to create one)?
   No, a resource needs to be created in a helper function of the resource iteself.

5. What is the type of the resource below?
  `Jacob`
```cadence
pub resource Jacob {

}
```

6. Let's play the "I Spy" game from when we were kids. I Spy 4 things wrong with this code. Please fix them.

```cadence
pub contract Test {

    // Hint: There's nothing wrong here ;)
    pub resource Jacob {
        pub let rocks: Bool
        init() {
            self.rocks = true
        }
    }

    pub fun createJacob(): Jacob { // there is 1 here
        let myJacob = Jacob() // there are 2 here
        return myJacob // there is 1 here
    }
}
```
Fixed
```cadence
pub contract Test {

    // Hint: There's nothing wrong here ;)
    pub resource Jacob {
        pub let rocks: Bool
        init() {
            self.rocks = true
        }
    }

    pub fun createJacob(): @Jacob { // there is 1 here
        let myJacob <- create Jacob() // there are 2 here
        return <- myJacob // there is 1 here
    }
}
```

## Chapter 3, Day 2

For today's quest, you'll have 1 large quest instead of a few little ones.

1. Write your own smart contract that contains two state variables: an array of resources, and a dictionary of resources. Add functions to remove and add to each of them. They must be different from the examples above.

```candence
pub contract PoemContract {
    pub var arrayOfPoems: @[Poem]
    pub var dictionaryOfPoems: @{Address: Poem}

    pub resource Poem {
        pub let text: String

        init(text: String) {
            self.text = text
        }
    }

    init() {
      self.arrayOfPoems <- []
      self.dictionaryOfPoems <- {}
    }

    pub fun addPoem(poem: @Poem) {
        self.arrayOfPoems.append( <- poem)
    }

    pub fun addPoemToAddress(addr: Address, poem: @Poem) {
        let oldPoem <- self.dictionaryOfPoems[addr] <- poem
        destroy  oldPoem
    }

    pub fun removePoem(index: Int): @Poem {
        return <- self.arrayOfPoems.remove(at: index)
    }

    pub fun removePoemFromAddress(addr: Address) {
        destroy self.dictionaryOfPoems.remove(key: addr)
    }

}
```

## Chapter 3, Day 3

1. Define your own contract that stores a dictionary of resources. Add a function to get a reference to one of the resources in the dictionary.

https://play.onflow.org/cb3fc946-9e94-4000-9181-a49967a39e44

```cadence
pub contract ResourceStuff {

    pub let dictionaryOfPets: @{String: Pet}

    pub resource Pet {
      pub(set) var name: String

      init(_name: String) {
        self.name = _name
      }
    }

    pub fun createPet(name: String): @Pet {
        return <- create Pet(_name: name)

    }

    pub fun changePetName(pet: &Pet) {
      pet.name = "alice"
    }

    pub fun addToDictionary(key: String, pet: @Pet) {
      self.dictionaryOfPets[key] <-! pet
    }

    pub fun getCharlieRef(): &Pet {
      return (&self.dictionaryOfPets["charlie"] as &Pet?)!
    }


    init() {
      self.dictionaryOfPets <- {}

      let pet <- self.createPet(name: "charlie")
      self.addToDictionary(key: pet.name, pet: <- pet)

    }
}
```

1. Create a script that reads information from that resource using the reference from the function you defined in part 1. 
```
import ResourceStuff from 0x01
pub fun main() {
  let charlie = ResourceStuff.getCharlieRef()
  log(charlie.name)

}
```

2. Explain, in your own words, why references can be useful in Cadence.
References are a way to read information on the blockchain without moving or providing priviledged access to it.  It can be useful for things like displaying metadata or nft images.