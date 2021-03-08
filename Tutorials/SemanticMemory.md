# Semantic memory:


Long-term identifiers (LTIs) are identifiers that exist in semantic memory only. Each LTI is labeled with an @ and a number (@5, @7, etc). The number assigned to it is permanent. LTIs never exist in working memory, but can be added to working memory as a short-term identifiers (STIs), through a query or retrieval. 


Each time you load an LTI into working memory, the STI created will always be unique. This means that if the same LTI is retrieved multiple times, each retrieval points to a unique STI instance in working memory. STIs can be modified in working memory, but this has no effect on the linked LTI in semantic memory. 

![Smem Introduction](./images/smem1.png)

## Retrieving Semantic Knowledge:

### Cue-Based Retrievals

A cue-based retrieval performs a search for a long-term identifier in semantic memory whose attribute/value pairs exactly match a query. The result is available to use on the command link. 


With cue based retrieval, you attach a query to the semantic memory's command link. On that query you can specify any attribute/value pair(s) that you want satisfied. 

![Smem Introduction](./images/smem2.png)
													         
	
		sp {propose*cue-based-retrieval                         
		    (state <s> ^superstate nil
		    		  -^name)
		-->
			(<s> ^operator <o>)
			(<o> ^name cue-based-retrieval)
		}					

When the rule is executed Soar looks through semantic memory for any LTI's that match the query, and if it's successful will make the results available. 

		sp {apply*cue-based-retrieval 
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

![Smem Introduction](./images/smem3.png)


After pulling the results of the query, we can place it into working memory, by attaching the STI on an attribute ^semantic-query-results on state s. 

![Smem Introduction](./images/smem4.png)

		
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



Alternatively, you do not need to store the results in working memory if you don't want to. If you know the structure of the semantic memory that you pulled in, you can access attribute/values directly off the smem.results.retrieved link.  



### Non-cue based retrieval


With non-cue based retrievals, you are searching semantic memory for a LTI. 

![Smem Introduction](./images/smem5.png)

![Smem Introduction](./images/smem6.png)

![Smem Introduction](./images/smem7a.png)


