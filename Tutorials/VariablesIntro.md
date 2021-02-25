### Variables in production rules 
Variables are represented in between angle brackets <>. Variables represent the value that you want to bind to an attribute. 

		sp {propose*eatFruit
			(state <s> ^name meals)
			(<s> ^person <man>)
			(<man> ^hungry true)
		--> 
			(<s> ^operator <o> +)
			(<o> ^name eatFruit)
		}
		
		In this example, we are binding the variable <man> to the attribute ^person. 



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
		
		 In this example, we are checking for the existence of an attribute
		 ^number1 that we bind to the variable <num1>. 
		 We then check to see if there is another attribute ^number2
		 and check to see if the value is greater than or equal to the value <num1> 
		 and binding the value to the variable <num2>. 




#### Disjunctions of values

Another noteworthy feature in Soar is to check for a disjunction of values. 


In Soar, you can check that an identifier, attribute, or value matches one of multiple constants. The syntax is uses double angled brackets << as follows:

<< A B C 45 I17 >>

<< 5 10 >>

<< good-morning good-evening >>

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
		
		In this code, we want to check working memory for a <fruit> that has an 
		attribute ^name equal to banana, peach, or pair,
		and assign that name to the variable <fname> for use later on. 


	
Note, you can only use constants inside the double brackets, you CANNOT use variables. 
		
	This is illegal: << <name> Bob >> 


