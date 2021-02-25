### Removing Working memory elements:

In the Right Hand Side (RHS) of soar production rule, it is possible to remove working memory attributes and values. To do this, add a dash (-) to the end of the attribute value. 


		sp {apply*selectFruit
			(state <s> ^fruit <fruit>)
		--> 
			(<s> ^fruit <fruit> -)
		}

This operator removes the attribute/value ^fruit/<fruit> from working memory elements. 

