## SIP-18: Modularizing Language Features

By Jan McSparen and Dominic Fuller-Rowell

## Overview
  * Changes to Scala occur via Scala Improvement Process (SIPs)
  * Scala is shifting from a more esoteric language, to a mainstream one 
  * SIP 18 deals with modularizing language features to ensure long term viability of the language

## Motivation for SIP 18
  * Proposal adresses two potential problems related to creating high-level languages and DSLs that arise due to shifting user base:
   * Making Scala easier for new programmers to learn and use
   * Preventing language features from interacting in unforseen ways (especially as new ones are added over time)

## How it is now
 * All language features in Scala are available in any environment
  * Not all Scala programmers need to make use of every tool to create libraries
  * People are prone to misuse features they don't fully understand
  * Features that aren't needed for a given problem may interact with necessary ones and cause cryptic/unhelpful error messages

## Modularize postfixOps

# What functionality does it provide?


It allows you to use operator syntax in postfix position (without ".").

    List(1,2,3) tail

rather than

    List(1,2,3).tail

In this harmless example it is not a problem, but it can lead to ambiguities. This will not compile:

    val appender:List[Int] => List[Int] = List(1,2,3) ::: //add ; here
    List(3,4,5).foreach {println}

And the error message is not very helpful:

    value ::: is not a member of Unit

It tries to call the ::: method on the result of the foreach call, which is of type Unit. This is likely not what the programmer intended. To get the correct result, you need to insert a semicolon after the first line.

[1]

## Modularize postfixOps

# Why should postfixOps be modular?



## Modularize reflectiveCalls


## Modularize implicitConversions

# What functionality does implicitConversions provide?

    class StringRepeated(original: String){
    def *(times: Int) = {
     def multiply(times: Int, accumulated: String): String = {
       if(times > 1) multiply(times - 1, accumulated + original)
       else original + accumulated
     }
     multiply(times, "")
    }
    }

Line 2 defines the '*' method for the class StringRepeated. Now we can multiply strings with the cunning use of Implicit Conversions!

    val repeated = (new StringRepeated("nice")) * 3
    // == "nicenicenice"    

Pretty cool right?

[3]

## Modularize implicitConversions

# Why should implicitConversions be modular?

* "Implicit conversions are known to cause many pitfalls if overused. And we have noted a tendency to overuse them because they look very powerful and their effects seem to be easy to understand."

* "Also, in most situations using implicit parameters leads to a better design than implicit conversions."

## Modularize higherKinds


## Modularize existentials

## Works-Cited

[1] http://stackoverflow.com/questions/13011204/scalas-postfix-ops
[2] http://docs.scala-lang.org/sips/pending/modularizing-language-features.html
[3] http://scalada.blogspot.com/2008/03/implicit-conversions-magical-and.html


