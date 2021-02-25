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

		sp{apply*calculate-math-facts
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
