## SIP-18: Modularizing Language Features

By Jan McSparen and Dominic Fuller-Rowell

## Overview
  * Changes to Scala occur via Scala Improvement Process (SIPs)
  * Scala is shifting from a more esoteric language, to a mainstream one 
  * SIP 18 deals with modularizing language features to ensure long term viability of the language

## Motivation for SIP 18
  * Proposal adresses two potential problems that arise due to shifting user base:
   * Making Scala easier for new programmers to learn and use
   * Preventing language features from interacting in unforseen ways (especially as new ones are added over time)

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


## Modularize higherKinds


## Modularize existentials

## Works-Cited

[1] http://stackoverflow.com/questions/13011204/scalas-postfix-ops
[2] 


