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

2. Create a script that reads information from that resource using the reference from the function you defined in part 1. 

  ```
  import ResourceStuff from 0x01
  pub fun main() {
    let charlie = ResourceStuff.getCharlieRef()
    log(charlie.name)

  }
  ```

3. Explain, in your own words, why references can be useful in Cadence.

  References are a way to read information on the blockchain without moving or providing priviledged access to it.  It can be useful for things like displaying metadata or nft images.

## Chatper 3, Day 4

1. Explain, in your own words, the 2 things resource interfaces can be used for (we went over both in today's content)
  - To restrict access
  - To provide a implementation details/requirements on a struct or resource

2. Define your own contract. Make your own resource interface and a resource that implements the interface. Create 2 functions. In the 1st function, show an example of not restricting the type of the resource and accessing its content. In the 2nd function, show an example of restricting the type of the resource and NOT being able to access its content.
```cadence

access(all) contract HelloWorld {

   pub resource interface Animal {
       pub let name: String
   }

   pub resource Cat: Animal {

       pub let name: String

       pub fun petCat(): String {
         return "purr"
       }
       init(_name: String) {
            self.name = _name
       }
   }

   pub fun createCat(name: String): @Cat {
     return <- create Cat(_name: name)
   }

   pub fun createHouseCat(name: String): @Cat{Animal} {
    return <- create Cat(_name: name)
   }

   
}
```
transaction
```cadence
import HelloWorld from 0x01

transaction {

  prepare(acct: AuthAccount) {}

  execute {
    let cat <- HelloWorld.createCat(name: "garfield")
    log(cat.petCat())
    destroy cat

    let felix <- HelloWorld.createHouseCat(name: "felix")
    //felix.petCat()
    destroy felix
  }
}
```
https://play.onflow.org/e9aa4183-a688-4a59-810e-de5802124134?type=tx&id=26f754d2-94bf-49e5-97e8-b179e317d0f8&storage=none


3. How would we fix this code? 

```cadence
pub contract Stuff {

    pub struct interface ITest {
      pub var greeting: String
      pub var favouriteFruit: String
    }

    // ERROR:
    // `structure Stuff.Test does not conform 
    // to structure interface Stuff.ITest`
    pub struct Test: ITest {
      pub var greeting: String
    
      pub fun changeGreeting(newGreeting: String): String {
        self.greeting = newGreeting
        return self.greeting // returns the new greeting
      }

      init() {
        self.greeting = "Hello!"
      }
    }

    pub fun fixThis() {
      let test: Test{ITest} = Test()
      let newGreeting = test.changeGreeting(newGreeting: "Bonjour!") // ERROR HERE: `member of restricted type is not accessible: changeGreeting`
      log(newGreeting)
    }
}
```

fixed
```cadence
```cadence
pub contract Stuff {

    pub struct interface ITest {
      pub var greeting: String
      pub var favouriteFruit: String
      pub fun changeGreeting(newGreeting: String): String
    }

    // ERROR:
    // `structure Stuff.Test does not conform 
    // to structure interface Stuff.ITest`
    pub struct Test: ITest {
      pub var greeting: String
      pub var favouriteFruit: String
      pub fun changeGreeting(newGreeting: String): String {
        self.greeting = newGreeting
        return self.greeting // returns the new greeting
      }

      init() {
        self.greeting = "Hello!"
        pub favouriteFruit = "strawberry" 
      }
    }

    pub fun fixThis() {
      let test: Test{ITest} = Test()
      let newGreeting = test.changeGreeting(newGreeting: "Bonjour!") // ERROR HERE: `member of restricted type is not accessible: changeGreeting`
      log(newGreeting)
    }
}
```

## Chapter 3, Day 5

For today's quest, you will be looking at a contract and a script. You will be looking at 4 variables (a, b, c, d) and 3 functions (publicFunc, contractFunc, privateFunc) defined in `SomeContract`. In each AREA (1, 2, 3, and 4), I want you to do the following: for each variable (a, b, c, and d), tell me in which areas they can be read (read scope) and which areas they can be modified (write scope). For each function (publicFunc, contractFunc, and privateFunc), simply tell me where they can be called.

Variables
 - a: can be read/write in all 4 areas
 - b: can be read/write in all 4 areas
 - c: can be read/write in area 1,2,3
 - d: can be read/write in area a only

Functions
 -  publicFunc: can be called in all 4 areas
 -  contractFunc: can be called in area 1,2,3
 -  privateFunc: can be called in area 1 only
 

```cadence
access(all) contract SomeContract {
    pub var testStruct: SomeStruct

    pub struct SomeStruct {

        //
        // 4 Variables
        //

        pub(set) var a: String

        pub var b: String

        access(contract) var c: String

        access(self) var d: String

        //
        // 3 Functions
        //

        pub fun publicFunc() {}

        access(contract) fun contractFunc() {}

        access(self) fun privateFunc() {}


        pub fun structFunc() {
            /**************/
            /*** AREA 1 ***/
            /**************/
        }

        init() {
            self.a = "a"
            self.b = "b"
            self.c = "c"
            self.d = "d"
        }
    }

    pub resource SomeResource {
        pub var e: Int

        pub fun resourceFunc() {
            /**************/
            /*** AREA 2 ***/
            /**************/
        }

        init() {
            self.e = 17
        }
    }

    pub fun createSomeResource(): @SomeResource {
        return <- create SomeResource()
    }

    pub fun questsAreFun() {
        /**************/
        /*** AREA 3 ****/
        /**************/
    }

    init() {
        self.testStruct = SomeStruct()
    }
}
```

This is a script that imports the contract above:
```cadence
import SomeContract from 0x01

pub fun main() {
  /**************/
  /*** AREA 4 ***/
  /**************/
}
```