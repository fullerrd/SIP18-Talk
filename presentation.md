## SIP-18: Modularizing Language Features

By Jan McSparen and Dominic Fuller-Rowell

# Overview
  * Changes to Scala occur via Scala Improvement Process (SIPs)
  * Scala is shifting from a more esoteric language, to a mainstream one 
  * SIP 18 deals with modularizing language features to ensure long term viability of the language

# Motivation for SIP 18
  * Proposal adresses two potential problems related to creating high-level languages and DSLs that arise due to shifting user base:
   * Making Scala easier for new programmers to learn and use
   * Preventing language features from interacting in unforseen ways (especially as more and more are added over time) [4]

# How it is now
 * All language features in Scala are available in any environment
  * Not all Scala programmers need to make use of every tool to create high-level libraries and DSLs 
  * People are prone to misuse features they don't fully understand
  * Features that aren't needed for a given problem may interact with necessary ones and cause cryptic/unhelpful error messages
[2]
  
# Aims of SIP 18
  * Making language easier/safer to use for those learning the language
  * Providing an alternative mechanism for turning on and off compiler features (as opposed to the current use of compiler options)
  * Adding a low boundary to the use of potentially problematic features to discourage their unnecessary use by library developers
  * Modularising language features to enable them to be turned off or substituted to support platform/deployment needs or to facilitate development of alternative implementations of these features
  * Providing a mechanism to warn all developers of the use of certain potentially problematic features
  [5]


# SIP 18: The Big Idea
 * Will require developers to explicitly include the language features they want to use 
 * This is not a new idea as there are other languages that require something very similar
  * In C it looks like: ``` #include <path-spec> ```
  * In Java it looks like: ``` import package.class ```
 
# Specification: What it looks like (1)
  * Enable advanced/contentious language features explicitly using an import from a new language enumeration object [2]        

```
     object languageFeature {  
       trait dynamics  
       trait postfixOps  
       trait reflectiveCalls  
       trait implicitConversions  
       trait higherKinds  
       trait existentials  
       object experimental {  
          trait macros  
       }  
     }
```

# Specification: What it looks like (2)
 * There is also an object called language in Scala that contains implicit feature values:

```
    object language {  
      import languageFeature._   
      implicit val macros: macros = _  
      implicit val dynamics: dynamics = _   
      implicit val postfixOps: postfixOps = _   
      implicit val reflectiveCalls: reflectiveCalls = _  
      implicit val implicitConversions: implicitConversions = _  
      implicit val higherKinds: higherKinds = _  
      implicit val existentials: existentials = _  
      object experimental {
        implicit val macros: macros = _
      }  
    }
```

# Specification: What it looks like (3)
 * The types in the languageFeature object are called feature flags. Each flag controls a set of features in Scala
 * To enable a feature an implicit value of the feature type must be available.  
 * This occurs with a named import from the language object:
 * For Example:

  ``` import language.experimental.macros  ```  
  or  
  ```  import language.{reflectiveCalls, existentials} ```
    

# Modularize postfixOps

## What functionality does postfixOps provide?

It allows you to use operator syntax in postfix position (without ".").

```List(1,2,3) tail``` rather than ```List(1,2,3).tail```  

In this harmless example it is not a problem, but it can lead to ambiguities. This will not compile:

    val appender:List[Int] => List[Int] = List(1,2,3) ::: //add ; here
    List(3,4,5).foreach {println}

And the error message is not very helpful:

    value ::: is not a member of Unit

[1]

# Modularize postfixOps

## Why should postfixOps be modular?

    val appender:List[Int] => List[Int] = List(1,2,3) ::: //add ; here
    List(3,4,5).foreach {println}

And the error message is not very helpful:

    value ::: is not a member of Unit

It tries to call the ::: method on the result of the foreach call, which is of type Unit. This is likely not what the programmer intended. To get the correct result, you need to insert a semicolon after the first line.


# Modularize implicitConversions

## What functionality does implicitConversion provide?

    class StringRepeated(original: String){
    def *(times: Int) = {
     def multiply(times: Int, accumulated: String): String = {
       if(times > 1) multiply(times - 1, accumulated + original)
       else original + accumulated
     }
     multiply(times, "")
    }
    }

Now we can multiply strings with the cunning use of Implicit Conversions!

    val repeated = (new StringRepeated("nice")) * 3
    // == "nicenicenice"    

Pretty cool right?

[3]

# Modularize implicitConversions

## Why should implicitConversions be modular?

* "Implicit conversions are known to cause many pitfalls if overused. And we have noted a tendency to overuse them because they look very powerful and their effects seem to be easy to understand."

* For example, the compiler doesn't tell you if you attempt to multiply two objects of different types and instead the script "just sings logically incorrect bytecode." 

* "Also, in most situations using implicit parameters leads to a better design than implicit conversions."

[3]

# Modularize higherKinds
## What functionality does higherKinds provide?

* higherKinds allow for the abstraction of types that are themselves abstract.

                           proper    first-order           higher-order

        values             10        (x: Int) => x         (f: (Int => Int)) => f(10)
        types (classes)    String    List                  Functors and Monads
        
* Abstraction over types do not loose their ability to be abstrated over in languages that support higer-order abstraction.

[6]

# Modularize higherKinds
## Why should higherKinds be modular?

* "Higher kinded types in Scala lead to a Turing-complete type system"
* Basicly this means that the language includes recursive functionality that would send the compiler into an infinite loop.
* "They tend to be useful mostly for typelevel computation and for a relatively narrow set of highly generic design patterns."
* Generic design patterns are almost always complex.
* These consepts tend to puzzle new-comers

# Modularize existentials
```def doStuff(arg : Array[T forSome { type T <: Foo }]) = stuff; ```

* This uses Scala's existential types to specify that there's an unknown type for which this holds true. 
* This is also possible in Java using wildcards
```def doStuff(arg : Array[T forSome { type T <: Comparable[T]}]) = stuff;```
* You can express more complicated type relationships than in Java (using wildcards).
*  “Existential types [are] … allowed in inferred types of values or return types of methods.”

[7]
# Modularize existentials
## Why should existentials be modular?
* Complex existential types can easily produce type errors.
* This is due to the abstract nature of existential typing.
* Misuse of existential types can result in “obscure error messages. Therefore, going overboard with existential types is generally perceived not to be a good idea.”
* In fact, they can be so problematic that they might not be included in a future release.


# SIP 18: Looking to the Future
  * This SIP is important to the long term viablilty of Scala
  * As Scala continues to grow controlling language features will become more and more critical 
   * SIP 18 preemptively addresses the possible problems related to new users, improper feature use, and unexpected feature interaction
  * As time goes on critics (there are many) will realize that without the adoption of this SIP 18 Scala would become hard to use and eventually stagnate

# Works-Cited

* [1] http://stackoverflow.com/questions/13011204/scalas-postfix-ops
* [2] http://docs.scala-lang.org/sips/pending/modularizing-language-features.html
* [3] http://scalada.blogspot.com/2008/03/implicit-conversions-magical-and.html
* [4] https://groups.google.com/forum/#!msg/scala-language/PV4q6O1qIh8/yG4p8PA2Jf8J
* [5] http://comments.gmane.org/gmane.comp.lang.scala.sips/472
* [6] http://stackoverflow.com/questions/6246719/what-is-a-higher-kinded-type-in-scala
* [7] http://unenterprise.blogspot.com/2008/01/variance-of-type-parameters-in-scala.html


