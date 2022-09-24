## Chapter 5, Day 1

1. Describe what an event is, and why it might be useful to a client.

    An event is something that occurs at a point in time that an application can listen to.  It is a good way to update the UI as things happen on the blockchain and provides a better user experience having to refresh the page or less expensive than polling.

2. Deploy a contract with an event in it, and emit the event somewhere else in the contract indicating that it happened.
https://play.onflow.org/1d79e3c6-d28e-40a4-93b6-3e07c910158c
    ```cadence
    pub contract HelloWorld {

        // Events
        pub event NFTCreated(id: UInt64)

        pub resource NFT {
            pub let id: UInt64

            init() {
                self.id = self.uuid
            }
        }

        pub fun createNFT(): @NFT {
            let nft <- create NFT()
            emit NFTCreated(id: nft.id)

            return <- nft
        }

    }
    ```
3. Using the contract in step 2), add some pre conditions and post conditions to your contract to get used to writing them out.
    ```cadence
    pub contract HelloWorld {

      // Events
      pub event NFTCreated(id: UInt64)

      pub resource NFT {
          pub let id: UInt64

          init() {
              post {
                  self.id == self.uuid: "The id does not equal the uuid"
              }
              self.id = self.uuid
          }
      }

      pub fun createNFT(): @NFT {
          let nft <- create NFT()
        emit NFTCreated(id: nft.id)

        return <- nft
      }

    }
    ```

  4. For each of the functions below (numberOne, numberTwo, numberThree), follow the instructions.

      ```cadence
      pub contract Test {

        // TODO
        // Tell me whether or not this function will log the name.
        // name: 'Jacob'

        // Yes, it logs Jacob because 
        pub fun numberOne(name: String) {
          pre {
            name.length == 5: "This name is not cool enough."
            }
            log(name)
          }

          // TODO
          // Tell me whether or not this function will return a value.
          // name: 'Jacob'

          // Yes because its returning "Jacob" + " Tucker"
          pub fun numberTwo(name: String): String {
            pre {
              name.length >= 0: "You must input a valid name."
            }
            post {
              result == "Jacob Tucker"
            }
            return name.concat(" Tucker")
          }

          pub resource TestResource {
            pub var number: Int

            // TODO
            // Tell me whether or not this function will log the updated number.
            // Also, tell me the value of `self.number` after it's run.

            // No it will not log anything because there is no log function.
            // After its run the first time, `self.number` will still be 0 because it fails. withouth the post condition it would be 1
            // the post should be:
            // before(self.number) + 1 == result
            pub fun numberThree(): Int {
              post {
                before(self.number) == result + 1
              }
              self.number = self.number + 1
              return self.number
            }

            init() {
              self.number = 0
            }

          }

        }
        ```
