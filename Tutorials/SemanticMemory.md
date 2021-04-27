# Overview of Semantic Memory:

Another memory in the Soar Architecture is semantic memory. This is used for storing long term knowledge and facts. For example 'red is a color', 'fire is hot', 'the sky is blue'. It would be inappropriate to store this type of memory in the Working Memory, as this knowledge is not always necessary.

Before we begin, be sure to **Enable** the semantic memory as it is <ins>disabled by default</ins>. The Soar Data loader enables it automatically, but if you are using the stock debugger, run the following command:

```
smem --set learning on
```


### How would you store this type of information? 

Surprisingly, it takes on a format similar to the structure of working memory. Soar's semantic memory utilizes a graph structure to store knowledge. Unlike the Working Memory, the Semantic Memory can store a series of multiple disconnected graphs. These graphs are not connected to the working memory initially. 

![Smem LTI](./images/smem0.png)

Soar constructs the Semantic Memory out of identifiers. Similar to the Working Memory Identifiers, these are 'nodes' that help layout the data. Identifiers in the Semantic Memory are referred to as Long Term Identifiers (LTI). 

These exist only in the Semantic Memory. Each LTI is labeled with an @ and a number (@5, @7, etc), the number assigned to it is permanent. LTIs never exist in working memory, but can be added to working memory as a short-term identifiers (STIs), through a query or direct retrieval. 

#### Adding/Storing Information in Semantic Memory

 The simplest way to add Semantic memory to Soar is with a command in the debugger:

```
smem --add {
(<n1> ^name alice ^friend <n2>)
(<n2> ^name bob ^friend <n1> <n3>)
(<n3> ^name charley)}
```

The format above is relatively simple to follow, with new variables being created, and attribute/value and attribute/id pairs being used to create the graph. This information can also be stored in the .sem file within the Soar Data loader. Only include the information in the parentheses. Be sure to also not include blank lines, as variables won't be utilized between sections that are split.

 Another way to add semantic memory is by utilizing the 'store' link. The following command can be placed in the 'action' section of a rule:

```
<s> ^smem.command.store <identifier>
```

This command will store the identifier, as well as a value linked to it.

Depending on the id, one of the following occurs:

* If the ```<identifier>``` has no current LTI linked, A new LTI is created
* If the ```<identifier>``` is linked to an LTI, the LTI in the semantic memory is overwritten

*Important note: All of semantic memory storing occurs just before the 'Output' phase in the decision making cycle.*


### How would you retrieve this type of information? 

How are we able to retrieve this knowledge from the semantic memory? There are multiple methods we will discuss in detail, but both methods rely on built-in links in working memory. The standard graph of working memory shows an attribute ^smem. Branching off ^smem we find attributes ^command and ^result.

![Smem Introduction](./images/shared-standard-working-memory.jpg)
	
- On the ^command attribute you can specify the LTI you want to grab, or conditions the LTI must satisfy. 
- On the ^results attribute, the LTI grabbed by the ^command attribute are placed. 


Each time you load an LTI into working memory, the STI created will always be unique. This means that if the same LTI is retrieved multiple times, each retrieval points to a unique STI instance in working memory. STIs can be modified in working memory, but this has no effect on the linked LTI in semantic memory. 

For simplicity, we will represent working memory, semantic memory, and the smem command and result links as seen below. However, keep in mind that the smem command and result links are built into the architecture of working memory. 

![Smem Introduction](./images/smem1.png)


## Retrieving Semantic Knowledge:

### Cue-Based Retrievals

When you don't know what LTI you want to grab, but instead have a criteria that an LTI must satisfy, you should use cue-based retrieval to access the contents of semantic memory. 

With cue-based retrieval, you attach a set of criteria to a query and attach the query to the command link. SOAR will look through semantic memory for an LTI that satisfies your criteria. If it is successful, the result is available to use on the command link. 

													         
	
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

![Smem Introduction](./images/smem2.png)


Before you can use the results of the query, it's a good idea to ensure that the query was successful. To ensure that the query was successful, check the smem.result.success link in the proposal operator.  The query results can then be added into working memory through, provided the query was successful.  


		sp {propose*access-query-results
		    (state <s> ^smem.result.success <cue>)
		-->
		    (<s> ^operator <o> +)
		    (<o> ^name access-query-results)
		}

Now when the rule is executed Soar looks through semantic memory for any LTI's that match the query, and if it's successful stores the LTI as an STI on the smem.results.retrieved link.

![Smem Introduction](./images/smem3.png)


After pulling the results of the query, we can place it into working memory, by attaching the STI on an attribute ^semantic-query-results on state ```<s>```. 

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

Once this is completed, you need to remove the query from the smem.command link in order to use it later.  



It is optional to store the results in working memory. Alternatively, if you know the structure of the semantic memory that you pulled in, you can access attribute/values directly off the smem.results.retrieved link.  



### Non-cue based retrieval


With non-cue based retrievals, you are searching semantic memory for a specific LTI. 

You place the lti on the link: ```<s> ^smem.command.retrieve <lti>```
	
If successful, the result will be placed on the link: ```<s> ^smem.command.retrieved <lti>```, and you will be notified on the success link: ```<s> ^smem.result.success <lti>```
	
If unsuccessful, a WME will be added to the ```<s> ^smem.result.failure <lti>```

![Smem Introduction](./images/smem5a.png)

![Smem Introduction](./images/smem6.png)

![Smem Introduction](./images/smem7a.png)


## Important additional Details
* Like the working memory, semantic memory is lost when the agent shuts down. This memory can be saved back to the disk as a SQLite file and loaded later. Information on this can be found in the Soar Manual
* ```<s> ^smem.result.success <identifier>``` will return a short term identifier linked to LTI that was just added
* Where the 'store' command might overwrite LTIs if they already match, Store-new creates a new LTI
* Storage/Retrieval occurs in parallel at every state, with only one command allowed per state.
* A 'depth' attribute can be used to retrieve memory structures at a greater depth
* The 'prohibit' command can insure that the retrieved LTI is not equal to the LTI you provide


## Learning Objective

Topic | Page Title
------------ | -------------
Recall the type of memory stored in the Semantic Memory	| 1 (remember)
Recall the structure used for the Semantic Memory | 1 (remember)
Understand the purpose of an LTI and the role it plays with the working memory | 2 (understand)
Understand the the usage of the Semantic memory WME links | 2 (understand)
Understand the ways of storing memory | 2 (understand)
Understand the different ways to retrieve Semantic memory | 2 (understand)
Recall which phase in the decision cycle semantic memory is retrieved | 1 (remember)

