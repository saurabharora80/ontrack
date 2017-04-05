1. Given these functions:
```
def  f1 : Future [ Unit ]  =  ??? 
def  f2 : Future [ Unit ]  =  ??? 
def  f3 : Future [ Unit ]  =  ??? 
def  f4 : Future [ Unit ]  =  ???
```

Write code to execute them when:
* there are no dependencies between the functions
    #### solution 
    ```
    val f1Val = f1
    val f2Val = f2
    val f3Val = f3
    val f4Val = f4
    
    val parallel = for {
          _ <- f1Val
          _ <- f2Val
          _ <- f3Val
          _ <- f4Val
        } yield Unit
    
    Await.result(parallel, Duration.Inf)    
    ```

* f4 depends on f3 which depends on f2 which depends on f1 
    #### solution 
    ```
    val sequencial = for {
          _ <- f1
          _ <- f2
          _ <- f3
          _ <- f4
        } yield Unit
    
    Await.result(sequencial, Duration.Inf)
    ```

* f4 depends on f3 and f2, and f3 and f2 both depend on f1
    #### solution 
    ```
    def f2And3 = {
          val f2Val = f2
          val f3Val = f3
          for {
            _ <- f2Val
            _ <- f3Val
          } yield Unit
        }
    
        val dependent: Future[Unit] = for {
          _ <- f1
          _ <- f2And3
          _ <- f4
        } yield Unit
    
        Await.result(dependent, Duration.Inf)
    ```

2. Given a list Seq(1, 2, 3)  which represents the number 123 , write a function to increment it by one without converting types. Your function should produce the expected result for the following test cases:
    ```
    Nil  =>  Nil
    Seq( 0 )  =>  Seq( 1 )
    Seq( 1 ,  2 ,  3 )  =>  Seq( 1 ,  2 ,  4 )
    Seq( 9 ,  9 ,  9 )  =>  Seq( 1 ,  0 ,  0 ,  0 )
    ```
    #### solution 
    ```
    def incrementByOne(numbers: Seq[Int]): Seq[Int] = {
      numbers match {
        case Nil => Nil
        case _ =>
          val lastNumbers = numbers.takeRight(1)
          val allOtherNumbers = numbers.dropRight(1)

          lastNumbers match {
            case Seq(9) if allOtherNumbers.isEmpty => (Seq(1, 0) :: Nil).flatten
            case Seq(9) => (incrementByOne(allOtherNumbers) :: Seq(0) :: Nil).flatten
            case Seq(_) => (allOtherNumbers :: lastNumbers.map(num => num + 1) :: Nil).flatten
          }
      }
    }
    ```
  
3. Describe 2 different ways of versioning REST API endpoints. Which is your preferred method and why.
    #### Option 1: The URI
    `/address-book/person/v1 or /address-book/v2/person`
    #### Option 2: Accept Header with versioned VND content type
    `Accept: application/vnd.ontrackretail.v2+json`
    
    > Niether approach is perfect, but I prefer the Accept header option as it keep the resource URL clean and         RESTFul by using extending an already existing standard HTTP content negotiation header. 

4. How would you design a REST API for an address book? What endpoints will it have (feel free to provide sample curl requests)? How would you handle errors?
    > See the [RAML](https://github.com/saurabharora80/ontrack/blob/master/ontrainretail.raml) 
        or open the [HTML](https://github.com/saurabharora80/ontrack/blob/master/ontrainretail.html) in a browser. 

5. Provide 2 examples of how you can safely use the string in this option:
    `val  myStringOption  =  Some ( "test" )`

    #### solution 
    ```
    val myStringOption = Some("test")

    myStringOption.map(println)

    myStringOption match {
      case Some(strVal) => println(strVal)
      case _ =>
    }
    ```
