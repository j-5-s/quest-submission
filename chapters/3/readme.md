## Quests

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
        return <-> myJacob // there is 1 here
    }
}
```
