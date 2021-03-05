Semantic memory:


Long-term identifiers (LTIs) are identifiers that exist in semantic memory only. Each LTI is labeled with an @ and a number (@5, @7, etc). The number assigned to it is permanent. LTIs never exist in working memory, but can be added to working memory as a short-term identifiers (STIs), through a query or retrieval. 

Each time you load an LTI into working memory, the STI created will always be unique. This means that if the same LTI is retrieved multiple times, each retrieval points to a unique STI instance in working memory. STIs can be modified in working memory, but this has no effect on the linked LTI in semantic memory. 



Retrieving Semantic Knowledge:

Cue-Based Retrievals

A cue-based retrieval performs a search for a long-term identifier in semantic memory whose attribute/value pairs exactly match a query. The result is available to use on the command link. In practice, you need to query the 


With cue based retrieval, you attach a query to the semantic memory's command link. On that query you can specify any attribute/value pair(s) that you want satisfied. 
													         
	
		sp {propose*cue-based-retrieval                         
		    (state <s> ^superstate nil
		    		  -^name)
		-->
			(<s> ^operator <o>)
			(<o> ^name cue-based-retrieval)
		}					

When the rule is executed Soar looks through semantic memory for any LTI's that match the query, and if it's successful will make the results available. 

		sp {propose*cue-based-retrieval 
			(state <s> ^operator <o>)
			(<o> ^name cue-based-retrieval)
			(<s> ^smem <smem>)  
			(<smem> ^command <cmd>)                 	 
		-->												
			(<cmd> ^query <q>)                            	
			(<q> ^attr1 val1                               		
			     ^attr2 <val2>                               	 
			     ^attr3 @V3 …)						     
		}



Before you can use the results of the query, it's a good idea to ensure that the query was successful.To ensure that the query was successful, check the smem.result.success link in the proposal operator.  The query results can then be added into working memory through, provided the query was successful.  


		sp {propose*access-query-results
		    (state <s> ^smem.result.success <cue>)
		-->
		    (<s> ^operator <o> +)
		    (<o> ^name access-query-results)
		}

Now when the rule is executed Soar looks through semantic memory for any LTI's that match the query, and if it's successful stores the LTI as an STI on the smem.results.retrieved link.


After pulling the results of the query, we place it into working memory, by attaching the STI on an attribute ^semantic-query-results on state <s>. 
		
		sp {apply*access-query-results
		    (state <s> ^operator.name access-query-results
		               ^smem <smem>)
		    (<smem> ^command <cmd>
		            ^result.retrieved <lti>)
		    (<cmd> ^query <q>)
		-->
		    (<s> ^semantic-query-results <lti>)
		    (<cmd> ^query <q> -)
		}

Once this is completed, you need to clear up smem command link in order to use it again (in the future).  



Alternatively, you do not need to store the results in working memory if you don't want to, you can instead access them directly off the smem.results.retrieved link and store the value on a variable, (without saving it in smem). 

For instance, 

								     <@7>
								       |
								       |
								     garage
								   /        \
								  /          \
								 /     	      \
								/              \
							   /		        \
							  /			         \
							prius              vespa
							/   \             /     \
						   / 	 \           / 	     \
				     passengers   mpg      passengers  mpg  
				      	 /        / 		 \		   \
				        /	 	 /			  \			\
				      5         35			   1		  50




Example: smem - person <p> 
				<p> ^firstname Katie
					^lastname Grondahl 




NON-cue based retrieval

