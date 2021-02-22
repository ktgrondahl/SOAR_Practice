Soar Syntax

I would only go through this after you feel comfortable with the format of working memory elements, and understand the basics of operators, attributes, 


### Operator preferences:

- Let's say you have two operators. (For this example, ignore the fact there are no apply operators.)  


		sp {propose*eatFruit
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> +)
			(<o> ^name eatFruit)
		}


		sp{propose*eatBreakfast}
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> +)
			(<o> ^name eatBreakfast)
		}


- If you run this program, and the conditions are satisfied (note the conditions are the same for both operators), soar will generate and Impasse, and you will be forced to create a substate in order to break the tie. Soar has no way of deciding which operator is preferential to apply. 

- Now if you modify the code to:

		sp {propose*eatFruit
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> + =)
			(<o> ^name eatFruit)
		}


		sp{propose*eatBreakfast}
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> + =)
			(<o> ^name eatBreakfast)
		}

- When you run this code, you will see that no impasse state is generated. The soar architecture will select an operator at random to apply. 

- Now lets say you want the eatBreakfast operator to be selected over the eatFruit operator. The following code will acheive just that: 

		sp {propose*eatFruit
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		-->
			(<s> ^operator <o> + )
			(<o> ^name eatFruit)
		}


		sp{propose*eatBreakfast}
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> + >)
			(<o> ^name eatBreakfast)
		}


- There is a ton more to discover to work out the subtleties of operator preference. One of the key takeaways is you can avoid, or intentionally trigger Impasses by making use of operator preferences.

### Documentation

- A production may contain an optional documentation string. The syntax for a documenta- tion string is that it is enclosed in double quotes and appears after the name of the production and before the first condition (and may carry over to multiple lines). The documentation string allows the inclusion of internal documentation about the production; it will be printed out when the production is printed using the print command.
. For example :

        sp {production-name 
        "Documentation string" 
        :type
        CONDITIONS
        -->
        ACTIONS
        }

### Variables in production rules 
Variables are represented in between angle brackets <var>. Variables represent the value that you want to bind to an attribute. 

		sp {propose*eatFruit
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> +)
			(<o> ^name eatFruit)
		}

In the previous example, we are binding the variable <man> to the attribute ^person. 

In Soar, you are able to test condition(s) for the value of an attribute before you bind the value to a variable. The conditions can be conjunctions or disjunctions. 

Consider this example in which we want to check that if the attribute ^number is greater than or equal to 3. :

		sp {propose*count
			(state <s> ^type state)
			(<s> ^number >= 3)
		-->
			(<s> ^operator <o> +)
			(<o> ^name count)

		}
 
This is pretty simply done, but lets say that you want to check if the value of the ^attribute counter is greater than or equal to three, and then bind the actual value to a new variable, for use and manipulation later. The syntax for this would be:

		sp {propose*count
			(state <s> ^type state)
			(<s> ^number { >= 3 <numval> })
		-->
			(<s> ^operator <o> +)
			(<o> ^name count)

		}

Note that the variable you are binding the value to occurs after the test condition. 

It's also possible to have multiple test conditions for a value. This is what is know as conjunctions in Soar.

Let's say we want the value to be greater than or equal to three and less than 100. The syntax is as follows: Note that all you do is add in another 'test' after the first one. 

		sp {propose*count
			(state <s> ^type state)
			(<s> ^number { >= 3 < 100 <numval> })
		-->
			(<s> ^operator <o> +)
			(<o> ^name count)

		}

Inside these conjunctions we can also perform checks on variables. Observe:

		sp {propose*count
			(state <s> ^type state)
			(<s> ^number1 <num1>)
			(<s> ^number2 { >= <num1> <num2> })
		-->
			(<s> ^operator <o> +)
			(<o> ^name count)

		}

In this example, we are checking for the existence of an attribute ^number1 that we bind to the variable <num1>. We are then checking to see if there is another attribute ^number2, checking to see if the value is greater than or equal to the value <num1> and binding the value to the variable <num2>. 


#### Disjunctions of values

Another noteworthy feature in Soar is to check for a disjunction of values. 


In Soar, you can check that an identifier, attribute, or value matches one of multiple constants. The syntax is uses double angled brackets << as follows:

<< A B C 45 I17 >>

<< 5 10 >>

<< good-morning good-evening >>

Note, you can only use constants inside the double brackets, you CANNOT use variables. This is illegal: '<< <name> Bob >>'. 

For example:

		sp {propose*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> ^name << banana pear peach >> )
		--> 
			(<s> ^operator <o> +)
			(<o> ^name SelectFruit)
		}

This operator examines wme and is looking to see if there is an element whose name matches banana, pear, or peach.

Let's say we want to save the value of ^name in a variable, but still ensure it matches one of the values in the disjunction. 
The solution is to use a conjunction enclosed in curly braces. 

		sp {propose*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> ^name { << banana pear peach >> <fname> })
		--> 
			(<s> ^operator <o> +)
			(<o> ^name SelectFruit)
		}

This code states that we want to check working memory for a <fruit> that has an attribute ^name equal to banana, peach, or pair, and assign that name to the variable <fname> for use later on. 



#### Negated Conditions:

Negated conditions can be tested in operator rules to check for the absense of values or patterns of values.  
		
		sp {propose*selectFruit
			(state <s> ^fruit <fruit>)
		   -(<fruit> ^name Banana)
		-->
		    ...
		}

This rule will only match if there is a value of the variable <fruit> whose name is NOT Banana. (A variable could be used in place of the Constant Banana, in order to test that there is no element fruit with the attribute name.)

It is important to avoid checking for the negation of a working memory element that is created with i-support. This would wind up in an infinite loop. 

Syntax for this looks like:

		sp {propose*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> -^name)
		-->
		    ...
		}

Note: It is syntactically invalid for the first condition of a rule to be a negated condition.

		sp {propose*selectFruit
			(state <s> -^fruit <fruit>)
		-->
		    ...
		}
 
In addtion to testing that a single conditon is not met, you can use a single negation to ensure that multiple conditions (a pattern of conditions does not exist)

		sp {propose*selectFruit
			(state <s> ^vehicle <car>)
		   -{(<car> ^make Toyota)
		     (<car> ^model Prius)
		     (<car> ^mpg 45)}
		-->
		    ...
		}


This rule checks working memory for an attribute ^vehicle that does not have attributes ^make, ^model, and ^mpg with values Toyota, Prius, and 45 respectively. 


Variables, predicates, disjunctions, and conjunctive tests can all be used in attributes. If you woudl like to learn more, please see pages 60 - 62 of the manual for examples. 


### RHS:

In the Right Hand Side (RHS) of soar production rule, it is possible to remove working memory attributes and values. To do this, add a dash (-) to the end of the attribute value. 


		sp {apply*selectFruit
			(state <s> ^fruit <fruit>)
		--> 
			(<s> ^fruit <fruit> -)
		}

This operator removes the attribute/value ^fruit/<fruit> from working memory elements. 


### Text input and Output:

A useful tool for debugging or displaying progess is to add print statements to your production rules. Print statements can be added to the RHS of production rules. The syntax for print statements is (write |string to print| <var>). 

		sp {apply*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> ^name { << banana >> <fname> })
		--> 
			(write |Selecting fruit: | <fname>)
		}

In the Soar debugger, when this rule is applied the following will print: 

Selecting fruit: banana. 

Note the variables can appear anywhere outside of the pipe characters. For example:


		sp {apply*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> ^name { << banana >> <fname> })
		--> 
			(write <fname> |is the next fruit we will select.| <fname> | is the selected fruit.| )
		}

This rule prints out: 
banana is the next fruit we will select. banana is the selcted fruit. 


To add a new line to a print statement write (clrf) where you would like the new line to occur:

		sp {apply*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> ^name { << banana >> <fname> })
		--> 
			(write <fname> |is the next fruit we will select.| (clrf) <fname> | is the selected fruit.| )
		}

This rule prints out:
 
banana is the next fruit we will select. 
banana is the selcted fruit. 

### Mathematical functions
In soar, mathematical operations utilize prefix notation, so the operator is placed at the begining of the expression. Note, you can perform mathematical operations on variables, constants, or a combination of the two.

 Take a look at the following examples:

- Single operation, traditional: (5 + 10). Soar: (+ 5 10)
- Nested operations, traditional: ((1 + 2) / (3 + 4)). Soar: (/ (+ 1 2) (+ 3 4))

There are also mathematical functions that may be of use to you:

- div, mod - div takes two integers and returns their integer quotient; mod takes two integers and returns their remainder. Variables can be used in place of constants.
- min, max - min takes a list of n integers and returns the lowest, max returns the smallest
- abs, atan2, sqrt, sin, cos - each function takes a single integer and returns the absolute value, square root, etc..

Take a look at the example rule for proper syntax:

		sp{apply*calculate
			...
		--> 
		   (<s> ^quotient (div <x> <y>))
		   (<s> ^remaindr (mod <x> <y>))
		   (<s> ^minimum (min <x> <y> 56 1))
		   (<s> ^maximum (max <x> <y> <z> 8 99)
		   (<s> ^Cosine (cos <x>)
		   )
		}

There are other mathematical functions you can make use of, for a more complete list please look at pages 73 - 75 in the manual. 
