### Operator preferences:

Let's say you have two operators. (For this example, ignore the fact there are no apply operators.)  


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


If you run this program, and the conditions are satisfied (note the conditions are the same for both operators), soar will generate and Impasse, and you will be forced to create a substate in order to break the tie. Soar has no way of deciding which operator is preferential to apply. 

Now if you modify the code to:

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

When you run this code, you will see that no impasse state is generated. The soar architecture will select an operator at random to apply. 

Now lets say you want the eatBreakfast operator to be selected over the eatFruit operator. The following code will acheive just that: 

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


There is a ton more to discover to work out the subtleties of operator preference. One of the key takeaways is you can avoid, or intentionally trigger Impasses by making use of operator preferences.

