#### Negated Conditions:

Negated conditions can be tested in operator rules to check for the absense of values or patterns of values.  
		
		sp {propose*selectFruit
			(state <s> ^fruit <fruit>)
		   -(<fruit> ^name Banana)
		-->
		    ...
		}

This rule will only match if there is a value of the variable fruite whose name is NOT Banana. (A variable could be used in place of the Constant Banana, in order to test that there is no element fruit with the attribute name.)

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

