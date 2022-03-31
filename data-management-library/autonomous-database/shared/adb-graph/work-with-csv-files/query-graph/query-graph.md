# Graph Studio: Query, visualize, and analyze a graph using PGQL and Java 

## Introduction

In this lab you will query the newly create graph (that is, `bank_graph`) in PGQL paragraphs of a notebook.

<!-- COMMENTED THE FOLLOWING OUT FOR DATABSE WORLD:
The following video shows some of the steps you will execute in this lab.

[](youtube:DLRlnw-NI1g) Graph Studio: Query a graph. -->

Estimated Time: 30 minutes. 

Watch the video below for a quick walk through of the lab.

[](youtube:XnE1yw2k5IU)

### Objectives

Learn how to
- use Graph Studio notebooks and PGQL and Java paragraphs to query, analyze, and visualize a graph.

### Prerequisites

- Earlier labs of this workshop. That is, the graph user exists and you have logged into Graph Studio. 

<!--
## Task 1: Verify that `BANK_GRAPH` is loaded into memory  

1. First check that the `BANK_GRAPH` is in memory. Click the **Graphs** menu icon 
   ![ALT text is not available for this image](images/radar-chart.svg "")
   and verify that `BANK_GRAPH` is in memory. If it isn't then click the action menu 
   ![ALT text is not available for this image](images/ellipsis-v.svg "")
   on that row and select **Load Graph into Memory**. 

   ![ALT text is not available for this image](images/load-bank-graph-into-memory.png " ")

-->

## Task 1: Import the notebook

The instructions below show you how to create each notebook paragraph, execute it, and change default visualization settings as needed.  
First **import** the sample notebook and then execute the relevant paragraph for each step in task 2.   

1. Download the exported notebook from the Object Store. 
   Use the following Pre-authenticated Request, or PAR, to download the exported notebook onto your machine.   
   Copy the URL below and paste it into your browser's address bar.  
   Note the location of the downloaded file. 

  <!--- old location 
  https://objectstorage.us-ashburn-1.oraclecloud.com/p/VEKec7t0mGwBkJX92Jn0nMptuXIlEpJ5XJA-A6C9PymRgY2LhKbjWqHeB5rVBbaV/n/c4u04/b/livelabsfiles/o/data-management-library-files/Learn_BankGraph_%20Find%20Circular%20Payments%20and%20Key%20Accounts.dsnb
  
  ---> 

	```
	<copy>
	https://objectstorage.us-ashburn-1.oraclecloud.com/p/VEKec7t0mGwBkJX92Jn0nMptuXIlEpJ5XJA-A6C9PymRgY2LhKbjWqHeB5rVBbaV/n/c4u04/b/livelabsfiles/o/data-management-library-files/circular-payments-notebook-wid770.dsnb
	</copy>
	```


2. Click the **Notebooks** menu icon and then on the **Import Notebook** icon on the top right.  

   ![ALT text is not available for this image](images/import-notebook-button.png " ")  

3. Drag the downloaded file or navigate to the correct folder and select it for upload.  
   ![ALT text is not available for this image](images/choose-exported-file.png " ")  

4. Click **Import**. 
   ![ALT text is not available for this image](images/notebook-file-selected.png " ")  
5. Once imported it should open in Graph Studio.  
   
   ![ALT text is not available for this image](images/notebook-imported.png " ")  

   You can execute the paragraphs in sequence and experiment with visualizations settings as described in **Task 2** below.  

<!---    
## Task 2: Create a notebook (Not necessary if you imported the notebook)
1. Click the **Notebooks** menu icon and then on **Create**, on the top right side of that page, to create a new notebook.  
Name it `Learn/BankGraph: Find Circular Payments`.  

   ![ALT text is not available for this image](./images/24-create-notebook.png " ")  

   The notebook will open to a blank paragraph.  

   ![ALT text is not available for this image](images/25-blank-notebook.png)

   Graph Studio notebooks currently support three type of paragraphs:  
      - `%md` for Markdown 
      - `%pgql-pgx` for Property Graph Query Language ([PGQL](https://pgql-lang.org)) 
      - `%java-pgx` for executing built-in graph algorithms with the Property Graph Java API  
  
2. Add a new paragraph. **Hover over the bottom middle part of the first paragraph**. Click the + icon 
   ![ALT text is not available for this image](images/plus-circle.svg "") when displayed.  
	![ALT text is not available for this image](images/28-add-new-paragraph.png " ") 

3. Let's enter a Markdown paragraph which outlines the notebook content. Copy and paste the following text into the first blank paragraph.

	```
	<copy>
	%md
	## Sample notebook to find circular payment chains and determine key accounts

	The `BANK_GRAPH` consists of ACCOUNTS as vertices and TRANSACTIONS as edges.  
	This notebook shows examples of finding patterns, such as circular payment chains, in the transactions and determining key accounts.  
	This is done in a few sets of steps:
	- Query and visualize 100 element of the `BANK GRAPH` to get an idea of the graph
	- List the top 10 accounts with the most number of transactions, that is, important accounts are those that have the most transactions.
	- Look for circular payments chains of specified length that originate at the top account in terms of number of transactions 
	- Compute the PageRank of the accounts to assess an alternate notion of importance
	- Compare the two notions of importance
	</copy>
	```

	Click the Run icon ![ALT text is not available for this image](images/play.svg "") to execute this paragraph.  

	Then click the Eye (visibility) icon 
	![ALT text is not available for this image](images/eye.svg") to turn off the Code listing and only display the result.   

	![ALT text is not available for this image](images/27a-md-turn-off-code-listing.png " ")  

 
--->

**Note:** *When you open a notebook it spawns a task to enable the interpreters which let you execute code snippets in a paragraph.*

That is, a notebook requires an environment, or compute, for interpreting and executing the lines of code entered in its paragraphs.  
Open the menu on the top-right corner to check whether the environment is attached, i.e. ready for use. 

![ALT text is not available for this image](images/env-attaching.png " ")

If the status is attaching you can choose to wait for the 20-30 seconds it may take to start up the environment. If the environment is attached it will be indicated by a green icon and the amount of memory (e.g. 14Gb) available for executing code and loading graphs into memory.  

![ALT text is not available for this image](images/env-attached.png " ")

## Task 2: Load and Query the `BANK_GRAPH` and visualize the results 

**Note:** *Execute the relevant paragraph after reading the description in each of the steps below*. 

1. First load the graph into the in-memory graph server since we will be executing some graph algorithms.  
   
   Run the first `%java-pgx` paragraph which uses the built-in `session` object to read the graph into memory from the database and creates a `PgXGraph` object which is a handle to the loaded graph.  

   The code snippet in that paragraph is:
   ```
   %java-pgx
	// The first step is to load the graph into the in-memory server
	// To do this we use the builtin session object 
	// We specify the graph by its name. The second argument to readGraphByName indicates that the graph is defined as a view on underlying database tables or views
	// And we store a handle to it in a PgxGraph object
	PgxGraph bankgraph = session.readGraphByName("BANK_GRAPH", GraphSource.PG_VIEW);
   ```

![ALT text is no available for this image](images/1-java-read-graph.png " ")  


**Note:** *If the compute environment is not ready as yet and the code cannot be executed then you will see a blue line moving across the bootom of the paragraph to indicate that a background task is in progress.  

![ALT text is not available for this image](images/env-not-ready.png " ")  


1. Next execute the paragraph which queries and displays 100 elements of the graph.    
   
	```
	%pgql-pgx
	/* Query and visualize 100 elements (nodes and edges) of BANK_GRAPH */
	select * 
	from match (s)-[t]->(d) on bank_graph 
	limit 100

	```

	The above PGQL query fetches the first 100 elements of the graph and displays them.  
	The MATCH clause specifies a graph pattern.  
	- `(s)` is the source node 
	- `[t]` is an edge 
	- `->` indicates the edge direction, that is, from the source `s` to a destination `d`
	- `(d)` is the destination node
	
	The LIMIT clause specifies the maximum of elements that the query should return.

	See the [PGQL site](https://pgql-lang.org) and specification for more details on the syntax and features of the language.  
	The Getting Started notebook folder also has a tutorial on PGQL.  

3. The result utilizes some features of the visualization component. The `acct_id` property is used for the node (or vertex) labels and the graph is rendered using a selected graph layout algorithm.  

**Note:** *You do not need to execute the following steps. They just outline the steps used. Feel free to experiment and modify the visualizations.* 

Steps required for customizing the visualization:  

   Click the visualization `settings` icon 
   ![ALT text is not available for this image](images/sliders.svg "") (the fourth icon from the left at the top of the visualization panel).  

   ![ALT text is not available for this image](images/31-viz-open-settings.png " ")   

    In this `Settings` dialog, click the **Customization** tab. Then scroll down and pick `ACCT_ID` from the `Labeling`, `Vertex Label` drop-down list.  

   ![ALT text is not available for this image](images/choose-viz-settings-label.png " ")  

   Click the **X** on the top-right to exit the Settings dialog. The resulting visualization should be similar to the screenshot below.   

     **Note:** The colors and layout shown in the screenshots may differ from those in your results.

	![ALT text is not available for this image](images/33-viz-labels-shown.png " ")   

   Now open the visualization settings again, click the **Customization** tab, and choose a different layout (**Concentric**) from the Layout drop-down list. Exit the Settings dialog. 

	![ALT text is not available for this image](images/concentric-layout-for-elements.png " ")

<!---
1. Add a Markdown paragraph describing the next step which is to look for circular transfers.   
    Create a new paragraph and enter the following text into it and execute it.   
	```
	<copy>
	%md
	### Find potential fraud patterns, for example, circular payment chain

	Circular payments chains are often of length between 5 and 7, that is, payments pass through 5 or 6 intermediate accounts before 
	landing back at the original account.   
	First list the top ten accounts in terms of number of incoming or outgoing transfers. 
	Then let's check if there are any such payment chains that start from one of those accounts, for example, account # 934.
	</copy>
	```

    ![ALT text is not available for this image](images/34a-md-circular-transfers.png " ")

--->

4. Next let's use PGQL to find the top 10 accounts in terms of number of transfers.  
	PGQL has built-in functions `IN_DEGREE` and `OUT_DEGREE` which return the number of incoming and outgoing edges of a node. So we can use them in this query.   
	Run the paragraph with the following query.  
	```
	%pgql-pgx
	/* List 10 accounts with the most number of transactions (that is, incoming + outgoing edges) */
	select a.acct_id, (in_degree(a) + out_degree(a)) as num_transactions 
	from match (a) on bank_graph 
	order by num_transactions desc 
	limit 10 
	```

	![ALT text is not available for this image](images/35-num-transfers-top-10-query.png " ")  

	We see that accounts 934 and 387 are high on the list.  

<!---
5.  This step is optional. It demonstrates some layout settings of the notebook.  
    Since the table has just two columns we may want to reduce its width and place two paragraphs and result side by side.  
	Click the **gear** icon at the top right to open the paragraph settings.  
	Move the **width slider** to about halfway or a little less.  

	![ALT text is not available for this image](images/35a-adjust-top-ten-para-width.png " ")  
--->

5.  Now check if there are any circular transfers originating and terminating at account 934.   
	Execute the following query.  
	```
	%pgql-pgx
	/* Check if there are any circular payment chains of length 5 from acct 934 */
	select *
	from match (a)-/:TRANSFERS{5}/->(a) on bank_graph 
	where a.acct_id=934
	```

	![ALT text is not available for this image](images/37-3rd-query-5-hops-from-934.png " ")

	Here `/:TRANSFERS{5}/` is a [reachability path expression](https://pgql-lang.org/spec/1.3/#reachability). It only tests for the existence of the path.  
	`:TRANSFERS` specifies that all edges in the path must have the label `TRANSFERS`.  
	While `{5}` specifies a path length of exactly 5 hops.  

	The result shows a dotted line which indicates a path, of length one or more, from the node for account 934 to itself.  
	
	![ALT text is not available for this image](images/38-3rd-query-result.png " ")
	
	It does not display all the paths or any intermediate nodes.

<!--- 
5. If you did not do step 5 to adjust the paragraph width then you needn't do this step either.  
   Adjust the width of the paragraph so that the top ten table query and the circular transfer query paragraphs are side by side.  

   	![ALT text is not available for this image](images/38a-adjust-width-circular-transfer.png " ")
--->

6. We can change the above query to include the node which made the deposit into account 934. This will display all the paths.   
	Execute the following query.  
	```
	%pgql-pgx
	/* Show the account that deposited into acct 934 in the 5-hop circular payment chain */
	select *
	from match (a)-/:TRANSFERS{4}/->(d)-[t]->(a) on bank_graph 
	where a.acct_id=934
	```

	![ALT text is not available for this image](images/39-4th-query-show-last-account-in-5-hop-chain.png " ")  

	The reachability test has paths of length four because we explicity specify the last hop (`(d)-[t]->(a)`).  

	![ALT text is not available for this image](images/40-4th-query-visualized.png " ")  

	Click the **Customization** settings and then select the **Concentric** layout and `ACCT_ID` for the vertex label.  

	![ALT text is not available for this image](images/choose-concentric-and-acctid.png " ")

7. The next query finds and displays the 6-hop circular payment chains originating at account 934.  
	
	  
	```
	%pgql-pgx
	/* Show the account that deposited into acct 934 in the 5-hop circular payment chain */
	select *
	from match (a)-/:TRANSFERS{5}/->(d)-[t]->(a) on bank_graph 
	where a.acct_id=934
	```

	![ALT text is not available for this image](images/42-5th-query-6-hops.png " ")  

	The resulting visualizing will be similar to the following screenshot.  

	![ALT text is not available for this image](images/43-5th-query-viz.png " ")  

8.  We may want also to display all the intermediate nodes, that is, accounts through which the money was transferred. 

	Let's do that for the 5-hop case.   
	```
	%pgql-pgx
	/* Show all the transfers in 5-hop circular payment chains starting from acct 934 */
	select a, t1, i1, t2, i2, t3, i3, t4, i4, t5 
	from match (a)-[t1]->(i1)-[t2]->(i2)-[t3]->(i3)-[t4]->(i4)-[t5]->(a) on bank_graph
	where a.acct_id=934
	```
	![ALT text is not available for this image](images/44-6th-query-show-all-5-hops.png " ")  


**Note:** *You do not need to execute the following steps. They just outline the steps used. Feel free to experiment and modify the visualizations.* 

Steps required for customizing the visualization:  

	This result is better viewed as a heirarchical layout.  
	Open the **Visualization** settings dialog and select **Hierarchical** from the **Layouts** drop-down list.  
	Leave the Direction as `Top-Down` and Algorithm Type as `Network Simplex`.

	![ALT text is not available for this image](images/choose-hierarchical-layout.png " ")  

    Use the **Settings** icon and **Customization** tab to label the vertices with the `ACCT_ID` for the resulting graph.   
	Lastly, let's change the source node's size to highlight it.   
	Open the **Visualization** settings dialog and select the **Highlights** tab.   
	Click **New Highlight**.   
	Then 
	- Click the **Size** checkbox and move the slider to `3X` so that the select vertex will be 3 times as larger as others.
	- Click the **+** icon next to `Condition` to add a selection criterion for the vertex.
	- Select `ACCT_ID` from the first drop-down list, `=` from the second, and `934.0` from the third to specify the criterion `ACCT_ID=934`.
	
	![ALT text is not available for this image](images/48-query-6-highlight-vertex-size.png " ")  

	The result will be similar to the following screenshot.  

	![ALT text is not available for this image](images/49-query-6-highlight-resulting-viz.png " ")

9. The remainder of this lab illustrates more query features and the use of the JAVA API to execute graph algorithms.  

<!---    
    Add a new Markdown paragraph, enter the following text, and exceute the paragraph.  

	```
	<copy>
	%md
	The queries above looked for paths of a specific length.  
	The next query looks for paths of length between 3 and 5 hops and let's the user enter the account number (that is, the source account in the circular chain) ay runtime.
	</copy>
	```

    ![ALT text is not available for this image](images/50-md-bind-params.png " ")
--->

10. This shows the use of bind parameters in a query. The account id value is entered at runtime.  
   **Enter 534 as the account id**, and then execute the paragraph.  

	```
	%pgql-pgx
	/* Check if there are any circular payment chains of between 3 and 5 hops starting from the user-supplied account # */
	select * 
	from match (a)-/:TRANSFERS{2,4}/->(d)-[t]->(a) on bank_graph
	where a.acct_id=${account_id}
	```

    ![ALT text is not available for this image](images/51-bind-params-in-query.png " ")

11. Now let's run the PageRank graph algorithm.  
 <!--- 
    Add a paragraph.  
	Paste the following Markdown text and execute it.  
    ```
	<copy>
	%md 

	[PageRank](https://en.wikipedia.org/wiki/PageRank) is a measure of relative importance.  Let's compute the PageRank for the accounts in our graph.  
	Algorithms are executed on the in-memory graph via the Java API for PGX.

	First we get a handle to the graph we wish to analyze, that is, `BANK_GRAPH`, from the implicitly created `session` object.   
	The `session`, `analyst`, and `instance` objects are implicitly created when the in-memory graph server is created. That is, when the environment is created.   
	Next we execute the PageRank algorithm, with default parameters, on the graph. The computed value is stored in an attrbute named `pagerank` that is added to each vertex.
	</copy>
	```

    ![ALT text is not available for this image](images/52-md-page-rank.png " ")
--->

    A %java-pgx paragraph lets you execute Java code snippets.  

	The code snippet below creates a PgxGraph object which contains a handle to the `BANK_GRAPH` which is loaded into the in-memory graph server.  
	Then it executes the `pagerank` algorithm using the built-in `analyst` Java object.  
	The `session` and `analyst` objects are created when the in-memory graph server is instantiated and when a notebook is opened.    

    Execute the paragrah containing the following code snippet.  
	```
	%java-pgx
	// PgxGraph bgraph = session.getGraph("BANK_GRAPH");
	analyst.pagerank(bankgraph);
	```

![ALT text is not available for this image](images/53-java-pagerank.png " ")

12.  Now let's use the computed PageRank value in visualizing a PGQL query result.  
 <!-- 
    First add a Markdown paragraph outlining the next step.  

	```
	<copy>
	%md
	Now query the graph again and use the PageRank value when symbolizing the account nodes.  
	Nodes with a PageRank above a ceratin value are displayed as larger circles.
	</copy>
	```

	![ALT text is not available for this image](images/54-md-use-pagerank-in-viz.png " ")
-->
	
 Execute the paragraph with the following query which finds the 6-hop transfers starting at account #934.  
    
	```
	%pgql-pgx
	/* Add highlights to symbolize account nodes by pagerank values. This shows that 934 is connected to other accounts with higher PageRank values. */
	SELECT *
	FROM MATCH(n)-/:Transfers{1,6}/->(m) on bank_graph
	WHERE n.acct_id = 934 limit 100
	```

**Note:** *You do not need to execute the following steps. They just outline the steps used. Feel free to experiment and modify the visualizations.*   

Steps required for customizing the visualization:  

   Click **Visualization** settings once the query has executed. Then click the **Highlights** tab.  
   ![ALT text is not available for this image](images/55a-highlights-for-pagerank.png " ")  

   Then click **New Highlight** and enter the following details to create it.  
   Specify pagerank >= 0.0035 as the condition, size = 3X as the visual effect.  

   ![ALT text is not available for this image](images/55b-new-hightlight-for-pagerank.png " ")  

   The result should be similar to the screenshot shown below.  

   ![ALT text is not available for this image](images/55-query-with-pagerank.png " ")


13.  Now let's compare the top ten accounts by PageRank and number of transactions.  

<!-- 
    Add a Markdown paragraph.  

    ```
	<copy>
	%md
	Now lets compare the two notions of importance: PageRank (relative importance of the accounts) and Aggregation (counting the number of transactions)
	</copy>
	```

	![ALT text is not available for this image](images/56-md-compare-top-ten.png " ")
-->

Execute the paragraph with the following query to show the top ten accounts by PageRank.  
 
    ```
	%pgql-pgx
	/* List top ten accounts by pagerank */
	select a.acct_id, a.pagerank
	from match (a) on bank_graph 
	order by a.pagerank desc limit 10
	```

    Click the **Table** icon to visualize the results as a table, if necessary.  

	![ALT text is not available for this image](images/57-top-ten-pagerank.png " ")    

14.  And the one which shows top ten accounts by number of transfers.  

    ```
	%pgql-pgx
	/* List 10 accounts with the most number of edges (that is, transfers) */
	select a.acct_id, in_degree(a) + out_degree(a) as num_transfers 
	from match (a) on bank_graph 
	order by num_transfers desc limit 10
	```

	![ALT text is not available for this image](images/58-top-ten-num-transfers.png " ")    

15. Account #222 is in the top ten by PageRank but not by # of transfers. So let us look at that account and its immediate neighbors in the graph.  
 <!--     
    Add a markdown paragraph.

	```
	<copy>
	Notice that account **#222** is in the top ten list by PageRank but not in the list by count of # of transactions.   
	So lets look at the immediate neighbors of it and symbolize them by pagerank with large nodes having higher values.  
	The immediate neighbors are those that made a deposit into account #222, or those that received a deposit from account #222.  

	We see that #222 has neighbors with a higher pagerank value and hence it itself has a high pagerank.
	</copy>
	```

    ![ALT text is not available for this image](images/59-md-account-222.png " ") 
-->

   Execute the paragraph which queries and displays account 222 and its neighbors. 

    ```
	%pgql-pgx
	/* show the transactions for acct id 222 */
	select * from match (v1)-[e1]->(a)-[e2]->(v2) on bank_graph where a.acct_id=222
	```


**Note:** *You do not need to execute the following steps. They just outline the steps used. Feel free to experiment and modify the visualizations.*   

Steps required for customizing the visualization:  

    Choose a **Grid** layout for the visualization.  
	Add a new highlight with pagerank >= 0.0035 as the condition, size = 3X as the visual effect.  
	 
![ALT text is not available for this image](images/60-account-222-and-neighbors.png " ")    
    
16.  Similarly account #4 has a higher PageRank but is not in the top 10 by #transfers while account #380 is in the top 10 by #transfers but not by PageRank.   
    So let us look at those two and their neighbors.  


<!--	First add a Markdown paragraph.  

    ```
	<copy>
	%md 

	Lastly, account **#4** is in the top ten list on the left (by PageRank) but not the right (# of transactions) while account **#380** is in the list on right but not the one on the left.  
	So lets us query and visualize those two accounts and their neighbors. And once again larger nodes have a higher pagerank.   
	We see that account #380 is mostly connected to accounts with lower pagerank values while account #4 is connected to accounts with a higher pagerank.

	</copy>
	```

	![ALT text is not available for this image](images/61-md-account-4-and-380.png " ")  

-->
Execute the pararaph which queries the neighbors of accounts #4 and #380.  

    ```
	%pgql-pgx
	/* Query and visualize elements (nodes and edges) of BANK_GRAPH for accts 4 and 380 */
	select * 
	from match (s)-[t]->(d) on bank_graph where s.acct_id = 4 or s.acct_id = 380 or d.acct_id = 4 or d.acct_id = 380
	```

**Note:** *You do not need to execute the following steps. They just outline the steps used. Feel free to experiment and modify the visualizations.*   

Steps required for customizing the visualization:  

    Change the graph visualization layout to **Hierarchical**. 

	![ALT text is not available for this image](images/62-viz-account-4-and-380.png " ")  	  



**Congratulations** on successfuly completing this workshop.

## Acknowledgements
* **Author** - Jayant Sharma, Product Management
* **Contributors** -  Jayant Sharma, Product Management
March* **Last Updated By/Date** - Jayant Sharma, Product Management, March 2022  
  
