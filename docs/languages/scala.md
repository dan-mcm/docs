# Scala

<p align="center">
  <img src="https://media.giphy.com/media/O2krcinMN6Pzq/giphy.gif"/>
</p>

## Variable Declaration

```Scala
// immutable
val donutsToBuy: Int = 5

// mutable
var favoriteDonut: String = "Glazed Donut"
favoriteDonut = "Vanilla Donut"

// lazy (delay the initialization until consumed by application)
// this also uses type inference - note the lack of : String!

lazy val donutService = "initialize some donut service"

// declare a variable with no initialization
var leastFavoriteDonut: String = _
leastFavoriteDonut = "Plain Donut"

```

## Supported Types
```Scala
val donutsBought: Int = 5
val bigNumberOfDonuts: Long = 100000000L
val smallNumberOfDonuts: Short = 1
val priceOfDonut: Double = 2.50
val donutPrice: Float = 2.50f
val donutStoreName: String = "allaboutscala Donut Store"
val donutByte: Byte = 0xa
val donutFirstLetter: Char = 'D'
val nothing: Unit = ()
```

## Collections

### Lists
```Scala
// List of Strings
val fruit: List[String] = List("apples", "oranges", "pears")

// List of Integers
val nums: List[Int] = List(1, 2, 3, 4)

// Empty List.
val empty: List[Nothing] = List()

// Two dimensional list
val dim: List[List[Int]] =
   List(
      List(1, 0, 0),
      List(0, 1, 0),
      List(0, 0, 1)
   )

// Basic Examples of common methods

// another way of defining the fruit List above using cons (::)
val fruit = "apples" :: ("oranges" :: ("pears" :: Nil))
val nums = Nil

println( "Head of fruit : " + fruit.head )
println( "Tail of fruit : " + fruit.tail )
println( "Check if fruit is empty : " + fruit.isEmpty )
println( "Check if nums is empty : " + nums.isEmpty )
```

### Sets
```Scala
// Empty set of integer type
var s : Set[Int] = Set()

// Set of integer type
var s : Set[Int] = Set(1,3,5,7)

or

var s = Set(1,3,5,7)

// find common elements between two sets

val num1 = Set(5,6,9,20,30,45)
val num2 = Set(50,60,9,20,35,55)

println( "num1.&(num2) : " + num1.&(num2) )
println( "num1.intersect(num2) : " + num1.intersect(num2) )
```

### Maps
```Scala
// Empty hash table whose keys are strings and values are integers:
var A:Map[Char,Int] = Map()

// A map with keys and values.
val colors = Map("red" -> "#FF0000", "azure" -> "#F0FFFF")

// Basic examples of common methods
val colors = Map("red" -> "#FF0000", "azure" -> "#F0FFFF", "peru" -> "#CD853F")

val nums: Map[Int, Int] = Map()

println( "Keys in colors : " + colors.keys )
println( "Values in colors : " + colors.values )
println( "Check if colors is empty : " + colors.isEmpty )
println( "Check if nums is empty : " + nums.isEmpty )
```

### Tuples
```Scala
// Short version
val t = (1, "hello", Console)

// Long version
val t = new Tuple3(1, "hello", Console)

// Example Usage
val t = (4,3,2,1)
val sum = t._1 + t._2 + t._3 + t._4
println( "Sum of elements: "  + sum )

val t = new Tuple3(1, "hello", Console)  
println("Concatenated String: " + t.toString() )
```

### Options
```Scala
// An Option[T] can be either Some[T] or None object, which represents a missing value.

// Example

object Demo {
   def main(args: Array[String]) {
      val capitals = Map("France" -> "Paris", "Japan" -> "Tokyo")

      println("capitals.get( \"France\" ) : " +  capitals.get( "France" ))
      println("capitals.get( \"India\" ) : " +  capitals.get( "India" ))
   }
}

// Example Output

capitals.get( "France" ) : Some(Paris)
capitals.get( "India" ) : None
```

### Iterators
```Scala
val it = Iterator("a", "number", "of", "words")

 while (it.hasNext){
    println(it.next())
 }

val ita = Iterator(20,40,2,50,69, 90)
val itb = Iterator(20,40,2,50,69, 90)

println("Maximum valued element " + ita.max )
println("Minimum valued element " + itb.min )
println("Value of ita.size : " + ita.size )
println("Value of itb.length : " + itb.length )
```


## Conditionals/Matching

### If Statements
```Scala
var x = 10;

if(x < 20)
  100
else if (x > 20)
  200
else
  0
```

### Pattern Matching
```Scala
import scala.util.Random

val x: Int = Random.nextInt(10)

x match {
  case 0 => "zero"
  case 1 => "one"
  case 2 => "two"
  case _ => "many"
}
```

```Scala
def matchTest(x: Int): String = x match {
  case 1 => "one"
  case 2 => "two"
  case _ => "many"
}
matchTest(3)  // many
matchTest(1)  // one
```

## Loops

### For

```Scala
var a = 0;

// for loop execution with a single range
for( a <- 1 to 10){
   println("Value of a: " + a );
}
```

```Scala
var a = 0;
var b = 0;

// for loop execution with multiple ranges
for( a <- 1 to 3; b <- 1 to 3){
   println( "Value of a: " + a );
   println( "Value of b: " + b );
}
```

```Scala
var a = 0;
val numList = List(1,2,3,4,5,6);

// for loop execution with a collection
for( a <- numList ){
   println( "Value of a: " + a );
}
```

```Scala
var a = 0;
val numList = List(1,2,3,4,5,6,7,8,9,10);

// for loop execution with multiple filters
for( a <- numList
     if a != 3; if a < 8 ){
   println( "Value of a: " + a );
}
```

### While

```Scala
var a = 10;

// while loop execution
while( a < 20 ){
   println( "Value of a: " + a );
   a = a + 1;
}
```
