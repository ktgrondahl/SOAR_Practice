### Text input and Output:

A useful tool for debugging or displaying progess is to add print statements to your production rules. Print statements can be added to the RHS of production rules. The syntax for print statements is (write |string to print|). You can print variables outside of the pipe characters.  

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


To add a new line to a print statement write (crlf) where you would like the new line to occur:

		sp {apply*selectFruit
			(state <s> ^fruit <fruit>)
			(<fruit> ^name { << banana >> <fname> })
		--> 
			(write <fname> |is the next fruit we will select.| (crlf) <fname> | is the selected fruit.| )
		}

This rule prints out:
 
	banana is the next fruit we will select. 
	banana is the selcted fruit. 

