## SIP-18: Modularizing Language Features

By Jan McSparen and Dominic Fuller-Rowell


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


