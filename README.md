## Functional Node Types

The Coalesce Functional Node Types Package includes:

* [Date Dimension](#date-dimension)
* [Pivot](#Pivot)
* [Unpivot](#Unpivot)
* [Match Recognize](#match-recognize)
* [View-Qualify advanced deploy](#view-qualify-advanced-deploy)
* [Code](#code)

---

## Date Dimension

The Coalesce Date Dimension Table provides a comprehensive breakdown of date-related attributes, enabling efficient handling of date operations across various
use cases. The table typically includes columns such as day, month, year, day of the week, week of the year, quarter, and flags like day is weekday
or weekend. Additional columns like fiscal year, fiscal quarter, holiday indicators can also be included, depending on the requirements.

### Date Dimension Node Configuration

The Date Dimension node type has two configuration groups:

* [Node Properties](#date-dimension-node-properties)
* [Options](#date-options)
* [Additional options](#additional-options)

![Fact_config](https://github.com/coalesceio/Coalesce-Base-Node-Types---Advanced-Deploy/assets/7216836/45d22ea5-32ca-49f5-a464-b266cb29b516)

#### Date Dimension Node Properties

| **Setting** | **Description** |
|----------|-------------|
| **Storage Location** | Storage Location where the WORK will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/> If FALSE the node will not be deployed or will be dropped during redeployment |

##### Date Options

| **Setting** | **Description** |
|---------|-------------|
| **Starting Date**| A date from where the date values should be added in the date table.Default is :DATEADD(DAY, -730, CURRENT_DATE)|
| **Number of Days To Generate ** | Numeric value indicating how many days' records should be generated from the Starting Date. |
| **Generated Date Column Name** |Metadata column name used in the SQL generated for inserting records into the table. |

#### Additional Options

You can create the node as:

* [Table](#date-table-create-as-table)
* [View](#date-table-create-as-view)
* [Transient Table](#date-table-create-as-transient-table)

##### Date Dimension Create as Table

| **Setting** | **Description** |
|---------|-------------|
| **Create As**| Table|
| **Truncate Before** | Toggle: True/False<br/>This determines whether a table will be overwritten each time a task executes. **True**: Uses INSERT OVERWRITE<br/>**False**: Uses INSERT to append data |
| **Insert Zero Key Record** | Toggle: True/False<br/>Insert Zero Key Record to Dimention if enabled |
| **Business key** | Required column for Type 1 Dimensions |
| **Default String Value** | If Insert Zero Key Record toggle is True then add a default value for columns with datatype string |
| **Default Surrogate Key Value** | If Insert Zero Key Record toggle is True then add a default value for surrogate key column|
| **Default Date Value (Date Format DD-MM-YYYY)** | If Insert Zero Key Record toggle is True then add a default value for date key column in the format DD-MM-YYYY|
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Pre-SQL**| SQL to execute before data insert operation |
| **Post-SQL** | SQL to execute after data insert operation |

##### Date Dimension Create as View

| **Setting** | **Description** |
|---------|-------------|
| **Create As**| View|
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Override Create SQL** | Toggle: True/False<br/>**True**: View is created by overriding the SQL<br/>**False**: Nodetype defined create view SQL will execute |

##### Date Dimension Create as Transient Table

| **Setting** | **Description** |
|---------|-------------|
| **Create As**| Transient Table|
| **Truncate Before** | Toggle: True/False<br/>This determines whether a table will be overwritten each time a task executes. **True**: Uses INSERT OVERWRITE<br/>**False**: Uses INSERT to append data |
| **Insert Zero Key Record** | Toggle: True/False<br/>Insert Zero Key Record to Dimention if enabled |
| **Business key** | Required column for Type 1 Dimensions |
| **Default String Value** | If Insert Zero Key Record toggle is True then add a default value for columns with datatype string |
| **Default Surrogate Key Value** | If Insert Zero Key Record toggle is True then add a default value for surrogate key column|
| **Default Date Value (Date Format DD-MM-YYYY)** | If Insert Zero Key Record toggle is True then add a default value for date key column in the format DD-MM-YYYY|
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Pre-SQL**| SQL to execute before data insert operation |
| **Post-SQL** | SQL to execute after data insert operation |


### Date Dimension Joins

Join conditions and other clauses can be specified in the join space next to mapping of columns in the UI.

![work_join](https://github.com/coalesceio/Coalesce-Base-Node-Types---Advanced-Deploy/assets/7216836/7acff10b-8845-4c44-851a-b7a0bc7eaf41)

> 📘 **Specify Group by and Order by Clauses**
>
> Best Practice is to specify group by and order by clauses in this space if you are not opting for the group by all and order by provided in OPTIONS config.

### Date Dimension Deployment

#### Date Dimension Initial Deployment

When deployed for the first time into an environment the Date node of materialization type table or view will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Date Table** | This will execute a CREATE OR REPLACE statement and create a table in the target environment |
| **Create Date View** | This will execute a CREATE OR REPLACE statement and create a view in the target environment |

#### Date Dimension Redeployment

After the Date node with materialization type table/transient table/view has been deployed for the first time into a target environment, subsequent deployments may result in either altering the Date Table or recreating the Date table.

#### Altering the Date Table and Transient Tables

A few types of column or table changes will result in an ALTER statement to modify the Persistent Table in the target environment, whether these changes are made individually or all together:

1. Changing table names
2. Dropping existing columns
3. Altering column data types
4. Adding new columns

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Rename Table/ Alter Column/ Delete Column/ Add Column/Edit table description** | Alter table statement is executed to perform the alter operation |

#### Date Dimension Recreating the Views

The subsequent deployment of Date node of materialization type view with changes in view definition, adding table description or renaming view results in deleting the existing view and recreating the view.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create View** | Creates a new view with updated definition |

#### Date Dimension Drop and Recreate View/Table/Transient Table

| **Change** | **Stages Executed** |
|------------|-------------------|
| **View to table/transient table** |  Drop view <br/> Create or Replace Date table/transient table |
| **Table/transient table to View** |  Drop table/transient table<br/> Create Date view |
| **Table to transient table or vice versa** |  Drop table/transient table<br/> Create or Replace Date table/transient table |

> 📘 **Materialization Date Dimension**
>
> When the materialization type of Date node is changed from table/transient table to View and use Override Create SQL for view creation. This ensures that the following change is made in the stage function in Create SQL tab so that the order of deployment is maintained.

![CreateSQL](https://github.com/coalesceio/Coalesce-Base-Node-Types---Advanced-Deploy/assets/7216836/0296abf8-0747-4ae8-8478-0782e5e2e545)

### Redeployment with no changes 

If the nodes are redeployed with no changes compared to previous deployment,then no stages are executed

### Date Dimension Deploy Undeployment

If a Date Dimension Node of materialization type table/view/transient table are deleted from a Datespace, that Datespace is committed to Git and that commit deployed to a higher level environment then the DateTable in the target environment will be dropped.

This is executed in below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop table/view** | Removes the table or view from the environment |

## Pivot

Pivoting ia crucial feature of data transformation.The [Pivot node](https://docs.snowflake.com/en/sql-reference/constructs/pivot) in Coalesce transforms a table by turning the unique values from one column in the input expression into multiple columns and aggregating results where required on any remaining column values. This operation is specified in the `FROM` clause after the table name or subquery.  

It is especially useful for converting narrow tables, such as one with columns for `empid`, `month`, and `sales`, 
into wider tables, for example, `empid`, `jan_sales`, `feb_sales`, and `mar_sales`.

### Pivot Node Configuration

Pivot has three configuration groups: 

* [Node Properties](#pivot-node-properties)
* [General Options](#pivot-general-options)
* [Pivot Options](#pivot-options)

  ![image](https://github.com/user-attachments/assets/edd67d5d-7216-429a-a292-2fe4980d1a9e)

### Pivot limitations

* Currently, the PIVOT semantic doesn’t allow multiple aggregations
* A pivot query that doesn’t use dynamic pivot can return output with duplicate columns. We recommend avoiding output with duplicate columns. A dynamic pivot query deduplicates duplicate columns.
* A pivot query that doesn’t use dynamic pivot might fail if it attempts to CAST a VARIANT column to a different data type. Dynamic pivot queries don’t have this limitation.
  
#### Pivot Node Properties

| **Property** | **Description** |
|--------------|-----------------|
| **Storage Location** | (Required) Storage Location where the Pivot Table will be created |
| **Node Type** | (Required) Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed/redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Pivot General Options

![image](https://github.com/user-attachments/assets/3e607c04-f5c8-40ed-8da8-018a5455f520)

| **Options** | **Description** |
|-------------|-----------------|
| **Create As** | Choose 'table', 'view' or 'transient table' |
| **Truncate** | True/False toggle to enable or disable truncating the output columns |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |

#### Pivot Options

##### Single Pivot Column

![image](https://github.com/user-attachments/assets/498aafd3-dba4-4d6e-92e7-ede9b54fef6a)

| **Options** | **Description** |
|-------------|-----------------|
| **Infer structure of Pivot table** | Toggle: True/False <br/> True,it is the first run and the pivot table structure is yet to be determined<br/>False,when the pivot table is created and generated columns have been Re-synced in Coalesce|
| **Pivot column**|Pivot column(Dropdown) <br/>Pivot column(textbox)The column from the source table or subquery that will be aggregated and turned into new columns.|
| **Single value column** |Toggle: True Determines which if analysis of single or multiple value columns to be added.Value column is the column from the source table or subquery that contains the values from which column names will be generated. |
|**Value Column**|-Value Column(Dropdown) <br/> -Value Column(textbox) <br/> Values you want to populate in the new columns.|
|**Aggregate Functions**|Aggregation you want to apply, like AVG, COUNT, MAX, MIN, and SUM.|
|**Subquery -PIVOT column values**|Not mandatory.A sql query is expected.When a query is mentioned,pivot happens on all values found in the subquery|
|**Filter Column Values(comma separated list of column values-Ex 'Q1','Q2')**|Not mandatory.Specified list of column values for the pivot column|
|**Exclude Columns**|Not mandatory.To specifically exclude columns from a pivot query|
|**Default value for NULL**|Replace all NULL values in the pivot result with the specified default value. The default value can be any scalar expression that does not depend on the pivot and aggregation column|

##### Multiple Pivot Columns

![image](https://github.com/user-attachments/assets/7a9eded6-0ca3-4ed3-9541-b25c943cca51)

| **Options** | **Description** |
|-------------|-----------------|
| **Infer structure of Pivot table** | Toggle: True/False<br/> True,it is the first run and the pivot table structure is yet to be determined.False,when the pivot table is created and generated columns have been Re-synced in Coalesce|
| **Pivot column**|Pivot column(Dropdown) <br/> Pivot column(textbox)<br/> The column from the source table or subquery that will be aggregated and turned into new columns.|
|**Pivot operation on same column values**|Toggle:True/False <br/>- True If pivot is to applied to same pivot column values for multiple value columns<br/>- False If pivot is to applied to differnt pivot column values for each value column|
| **Single value column** |Toggle:False Determines which if analysis of single or multiple value columns to be done.Value column is the column from the source table or subquery that contains the values from which column names will be generated. |
|**Value Column**|-Value Column(Dropdown) <br/> -Value Column(textbox)<br/> Values you want to populate in the new columns.<br/>-Aggregate Functions<br/>Aggregation you want to apply, like AVG, COUNT, MAX, MIN, and SUM.<br/>-Column Values <br/> Enabled if the Pivot operation on same column values is false|
|**Filter Column Values(comma separated list of column values-Ex 'Q1','Q2')**|Specified list of column values for the pivot column|
|**Default value for NULL**|Replace all NULL values in the pivot result with the specified default value. The default value can be any scalar expression that does not depend on the pivot and aggregation column|

### Pivot node Usage

* Add a Pivot node on top of source node
* Add the pivot columns,value columns ,aggregation operation from config
* When you choose the pivot and value dropdown,ensure that the textbox alongside the dropdown is entered with Column name.This textBox information is required once the pivot table structure is synced into Coalesce.
* The toggle 'Infer Structure of Pivot Data' is required to be true when the node is created for the first time.
* The toggle 'Single value column' is set to false, if you want a multi-dimensional pivot
* Once the pivot table is created,the 'Re-Sync Columns' can be used to sync the structure of pivot table into Coalesce mapping grid.
  ![image](https://github.com/user-attachments/assets/2f39a419-1662-41e3-9fda-eaaf08c8b1d3)
* After Re-sync,recreate the table with 'Infer Structure of Pivot Data' set to false
* If the above works, it should be deployable as is. Deploy will simply take the columns and execute a create table.
* Hit run to insert data into table keeping the 'Infer Structure of Pivot Data' set to false
  
### Pivot Deployment

### Points to note for deployment
* Create table with ‘Infer PIVOT structure’ toggle enabled
* Re-Sync columns to the mapping grid
* Deploy with ‘Infer PIVOT structure’ toggle set to false
* Repeat the above steps if you see changes in column of table during redeployment.It is fine to skip for change in materialization type,change in target location or change in node name
* Ensure the new columns added or dropped are part of the inferred PIVOT structure and not added/dropped directly in the mapping grid.The deployment will succeed but insert will fail
> 📘 **Deployment**
>
> Ensure 'Infer Pivot structure' set to false before deployment

#### Pivot Initial Deployment

When deployed for the first time into an environment the Pivot node of materialization type table or view will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Pivot Table** | This will execute a CREATE OR REPLACE statement and create a table in the target environment |
| **Create Pivot View** | This will execute a CREATE OR REPLACE statement and create a view in the target environment |

#### Pivot Redeployment

After the Pivot node with materialization type table/transient table/view has been deployed for the first time into a target environment, subsequent deployments may result in either altering the Pivot Table or recreating the Pivot table.
Pivot
#### Altering the  Table and Transient Tables

A few types of column or table changes will result in an ALTER statement to modify the Persistent Table in the target environment, whether these changes are made individually or all together:

1. Changing table names
2. Dropping existing columns
3. Altering column data types
4. Adding new columns

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Rename Table/ Alter Column/ Delete Column/ Add Column/Edit table description** | Alter table statement is executed to perform the alter operation |

#### Pivot Recreating the Views

The subsequent deployment of Pivot node of materialization type view with changes in view definition, adding table description or renaming view results in deleting the existing view and recreating the view.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Pivot View** | Creates a new view with upPivotd definition |

#### Pivot Drop and Recreate View/Table/Transient Table

| **Change** | **Stages Executed** |
|------------|-------------------|
| **View to table/transient table** |  Drop view <br/> Create or Replace Pivot table/transient table |
| **Table/transient table to View** |  Drop table/transient table<br/> Create Pivot view |
| **Table to transient table or vice versa** |  Drop table/transient table<br/> Create or Replace Pivot table/transient table |

### Redeployment with no changes 

If the nodes are redeployed with no changes compared to previous deployment,then no stages are executed

### Pivot Deploy Undeployment

If a Pivot Node of materialization type table/view/transient table are deleted from a workspace, that workspace is committed to Git and that commit deployed to a higher level environment then the PivotTable in the target environment will be dropped.

This is executed in below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop table/view** | Removes the table or view from the environment |

## Unpivot

The [Unpivot node](https://docs.snowflake.com/en/sql-reference/constructs/unpivot#examples) in Coalesce rotates a table by transforming columns into rows. 
UNPIVOT is not exactly the reverse of PIVOT because it cannot undo aggregations made by PIVOT.

This operator can be used to transform a wide table (e.g. empid, jan_sales, feb_sales, mar_sales) into a narrower table (e.g. empid, month, sales).

### Unpivot limitations

* It cannot reverse aggregations performed by PIVOT
* It requires that all columns have the same data type.In case if the columns from source have diffrent data types,ensure the data types are type casted in an upstream node before adding a UNPIVOT node.
* UNPIVOT cannot be used in dynamic tables or stored procedures
* Ensure that your data is structured and formatted correctly, as any inconsistencies may affect the unpivoting process. It's important to check for any missing values, duplicate entries, or data types that are not compatible with the unpivot function.

### Unpivot Node Configuration

Unpivot has three configuration groups: 

* [Node Properties](#unpivot-node-properties)
* [General Options](#unpivot-general-options)
* [Unpivot Options](#unpivot-options)

#### Unpivot Node Properties

| **Property** | **Description** |
|--------------|-----------------|
| **Storage Location** | (Required) Storage Location where the Pivot Table will be created |
| **Node Type** | (Required) Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed/redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

 ![image](https://github.com/user-attachments/assets/edd67d5d-7216-429a-a292-2fe4980d1a9e)

#### Unpivot general Options

![image](https://github.com/user-attachments/assets/3e607c04-f5c8-40ed-8da8-018a5455f520)

| **Options** | **Description** |
|-------------|-----------------|
| **Create As** | Choose 'table', 'view' or 'transient table' |
| **Truncate** | True/False toggle to enable or disable truncating the output columns |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |

#### Unpivot Options

![image](https://github.com/user-attachments/assets/45d4e9ae-8da3-46cf-b6bf-37ff693c8fa9)

| **Options** | **Description** |
|-------------|-----------------|
| **Infer structure of Pivot table** | Toggle: True/False<br/> True,it is the first run and the pivot table structure is yet to be determined.False,when the pivot table is created and generated columns have been Re-synced in Coalesce|
| **Value-Coulmn** |Column that will hold the values from the unpivoted columns |
| **Name-column** | Column that will hold the names of the unpivoted columns  |
| **Column-list**| The names of the columns in the source table or subquery that will be rotated into a single pivot column|
| **Include NULLS**| Specifies whether to include or exclude rows with NULLs|

### Unpivot node Usage

* Add a Unpivot node on top of source node
* Add the Unpivot column list ,value column,name column in config
* When you choose the Unpivot and value dropdown,ensure that the textbox alongside the dropdown is entered with Column name.This textBox information is required once the Unpivot table structure is synced into Coalesce.
* The toggle 'Infer Structure of Unpivot Data' is required to be true when the node is created for the first time.
* The toggle 'Single value column' is set to false, if you want a multi-dimensional Unpivot
* Once the Unpivot table is created,the 'Re-Sync Columns' can be used to sync the structure of Unpivot table into Coalesce mapping grid.
* After Re-sync,recreate the table with 'Infer Structure of Unpivot Data' set to false
  ![image](https://github.com/user-attachments/assets/79282085-e9b7-41e1-8bd2-68fdf98eeb00)
* If the above works, it should be deployable as is. Deploy will simply take the columns and execute a create table.
* Hit run to insert data into table keeping the 'Infer Structure of Pivot Data' set to false

#### Unpivot Initial Deployment

### Points to note for deployment
* Create table with ‘Infer UNPIVOT structure’ toggle enabled
* Re-Sync columns to the mapping grid
* Deploy with ‘Infer UNPIVOT structure’ toggle set to false
* Repeat the above steps if you see changes in column of table during redeployment.It is fine to skip for change in materialization type,change in target location or change in node name
* Ensure the new columns added or dropped are part of the inferred UNPIVOT structure and not added/dropped directly in the mapping grid.The deployment will succeed but insert will fail

> 📘 **Deployment**
>
> Ensure 'Infer Unpivot structure' set to false before deployment

When deployed for the first time into an environment the Unpivot node of materialization type table or view will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Unpivot Table** | This will execute a CREATE OR REPLACE statement and create a table in the target environment |
| **Create Unpivot View** | This will execute a CREATE OR REPLACE statement and create a view in the target environment |

#### Unpivot Redeployment

After the Unpivot node with materialization type table/transient table/view has been deployed for the first time into a target environment, subsequent deployments may result in either altering the Unpivot Table or recreating the Unpivot table.
Unpivot
#### Altering the  Table and Transient Tables

A few types of column or table changes will result in an ALTER statement to modify the Persistent Table in the target environment, whether these changes are made individually or all together:

1. Changing table names
2. Dropping existing columns
3. Altering column data types
4. Adding new columns

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Rename Table/ Alter Column/ Delete Column/ Add Column/Edit table description** | Alter table statement is executed to perform the alter operation |

#### Unpivot Recreating the Views

The subsequent deployment of Unpivot node of materialization type view with changes in view definition, adding table description or renaming view results in deleting the existing view and recreating the view.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Unpivot View** | Creates a new view with upUnpivotd definition |

#### Unpivot Drop and Recreate View/Table/Transient Table

| **Change** | **Stages Executed** |
|------------|-------------------|
| **View to table/transient table** |  Drop view <br/> Create or Replace Unpivot table/transient table |
| **Table/transient table to View** |  Drop table/transient table<br/> Create Unpivot view |
| **Table to transient table or vice versa** |  Drop table/transient table<br/> Create or Replace Unpivot table/transient table |

### Redeployment with no changes 

If the nodes are redeployed with no changes compared to previous deployment,then no stages are executed

### Unpivot Deploy Undeployment

If a Unpivot Node of materialization type table/view/transient table are deleted from a Unpivotspace, that Unpivotspace is committed to Git and that commit deployed to a higher level environment then the UnpivotTable in the target environment will be dropped.

This is executed in below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop table/view** | Removes the table or view from the environment |

## Match Recognize

The Match Recognize node type uses Snowflake's SQL MATCH_RECOGNIZE clause to identify and process patterns in datasets(https://docs.snowflake.com/en/sql-reference/constructs/match_recognize). It helps identify events, trends, and anomalies by analyzing rows in sequence. It is useful for tasks like fraud detection, session tracking, and clickstream analysis, with options to customize patterns for different needs.

### Match Recognize Node Configuration

Match Recognize has three configuration groups: 

* [Node Properties](#Match-Recognize-node-properties)
* [General Options](#Match-Recognize-general-options)
* [Match Recognize Options](#Match-Recognize-options)


#### Match Recognize Node Properties

| **Property** | **Description** |
|--------------|-----------------|
| **Storage Location** | (Required) Storage Location where the Match Recognize Table will be created |
| **Node Type** | (Required) Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed/redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Match Recognize General Options


| **Options** | **Description** |
|-------------|-----------------|
| **Create As** | Choose 'table', 'view' or 'transient table' |
| **Truncate** | True/False toggle to enable or disable truncating the output columns |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |

#### Match Recognize Infer Column Option

| **Options** | **Description** |
|-------------|-----------------|
| **Infer structure of Match Recognize table** | Toggle: True/False <br/> True,it is the first run and the Match Recognize table structure is yet to be determined<br/>False,when the Match Recognize table is created and generated columns have been Re-synced in Coalesce|

#### Match Recognize Partitioning & Ordering

| **Options** | **Description** |
|-------------|-----------------|
| **Partition By** | Toggle: True/False <br/> True,Enable the Column Dropdown and Textbox to add columns for partitioning<br/>False,Disable the Dropdown and Textbox to add columns|
| **Match Recognize Column**|Match Recognize column(Dropdown) <br/>Match Recognize column(textbox) The column from the source table or subquery that will be added in Partition By Clause of the Match Recognize Query|
| **Order By** | Toggle: True/False <br/> True,Enable the Column Dropdown and Textbox to add columns for adding in order by clause<br/>False,Disable the Dropdown and Textbox to add columns|
| **Match Recognize Column**|Match Recognize column(Dropdown) <br/>Match Recognize column(textbox)The column from the source table or subquery that will be added in Order By Clause of the Match Recognize Query|

#### Match Recognize Output Specification

##### Measures
| **Options** | **Description** |
|-------------|-----------------|
| **Expression** | Expression Textbox <br/> A function name to be added as part of major for e.g MATCH_NUMBER,COUNT etc|
| **Column Name (OR Value to pass to Expression)**|Column Textbox <br/>Acolumn name or the value to the function mentioned in the expression of measures of the Match Recognize Query|
| **Alias of Expression Column Name** | Alias Expression Textbox <br/> Alias of the expression  of measures of the Match Recognize Query|

#### Match Recognize Match Control
| **Options** | **Description** |
|-------------|-----------------|
| **Rows Per Match** | (Dropdown) <br/> Specifies which rows are returned for a successful match.Select ONE ROW PER MATCH , ALL ROWS PER MATCH or BLANK if not required
| **All Rows Per Match** | (Dropdown) <br/> Returns a row for each row that is part of the match. Select SHOW EMPTY MATCHES, OMIT EMPTY MATCHES , WITH UNMATCHED ROWS or BLANK if not required 
| **After Match Skip** | (Dropdown) <br/> Specifies where to continue after a match. Select PAST LAST ROW , TO NEXT ROW, TO or BLANK (if not required )
| **To** | (Dropdown) <br/> Continue matching at the first or last (default) row that was matched to the given symbol. Select  FIRST or LAST.
| **After match skip variable name** | (Textbox) <br/> Add an alias name for the after match skip clause.

#### Match Recognize Pattern & Conditions
| **Options** | **Description** |
|-------------|-----------------|
| **Pattern** | (Textbox) <br/> The pattern defines a valid sequence of rows that represents a match. The pattern is defined like a regular expression. (regex) and is built from symbols, operators, and quantifiers.

##### Define
| **Options** | **Description** |
|-------------|-----------------|
| **Expression** | (Textbox) <br/> Defining symbols (also known as “pattern variables”) are the building blocks of the pattern.A symbol is defined by an expression.The 
| **Column Name** | (Textbox) <br/> It is the symbol name of the expression


#### Match Recognize Select Query Options
| **Options** | **Description** |
|-------------|-----------------|
| **Select Query Functions** | Toggle: True/False <br/> True,Enable Expression and columns to add aggregate functions to Select Query<br/>False,Disable the option|
| **Expression** | Expression Textbox <br/> A function name to be added as part of major for e.g AVG,COUNT etc|
| **Column Name (OR Value to pass to Expression)**|Column Textbox <br/>Acolumn name or the value to the function mentioned in the expression of select quey of the Match Recognize Query|
| **Alias of Expression Column Name** | Alias Expression Textbox <br/> Alias of the expression  of select query of the Match Recognize Query|
| **Select Query Order By** | Toggle: True/False <br/> True,Enable the Column Dropdown and Textbox to add columns for adding in order by clause of select query<br/>False,Disable the Dropdown and Textbox to add columns|
| **Select Query Order By Column**|Select Query Order By column(Dropdown) <br/>Select Query Order By column(textbox).The column from the source table or subquery that will be added in Order By Clause of the Select Query|

### Match Recognize node Usage

* Add a Match Recognize node on top of source node
* Add the Match Recognize options from config
* When you choose the Match Recognize and value dropdown,ensure that the textbox alongside the dropdown is entered with Column name.This textBox information is required once the Match Recognize table structure is synced into Coalesce.
* The toggle 'Infer Structure of Match Recognize Data' is required to be true when the node is created for the first time.
* Once the Match Recognize table is created,the 'Re-Sync Columns' can be used to sync the structure of Match Recognize table into Coalesce mapping grid.
* For further Match Recognize operations,keep the 'Infer Structure of Match Recognize Data' set to false
  
### Match Recognize Deployment

### Points to note for deployment
* Create table with ‘Infer Structure of Match Recognize Data’ toggle enabled
* Re-Sync columns to the mapping grid
* Deploy with ‘Infer Structure of Match Recognize Data’ toggle set to false
* Repeat the above steps if you see changes in column of table during redeployment.It is fine to skip for change in materialization type,change in target location or change in node name
* Ensure the new columns added or dropped are part of the inferred Match Recognize structure and not added/dropped directly in the mapping grid.The deployment will succeed but insert will fail.

### Match Recognize Initial Deployment
When deployed for the first time into an environment the Match Recognize node of materialization type table or view or transient table will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Match Recognize Table/transient table/view** | This will execute a CREATE OR REPLACE statement and create a Match Recognize table in the target environment |

#### Match Recognize Table Redeployment

When the Match Recognize node is redeployed with any changes in table or config changes result in re-creating the node

The below stage is executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Match Recognize Table/transient table/view** | This will execute a CREATE OR REPLACE statement and create a Match Recognize table in the target environment |

#### Match Recognize Table Deploy Drop and Recreate Work View/Table/Transient Table

| **Change** | **Stages Executed** |
|------------|-------------------|
| **View to table/transient table** |  Drop view <br/> Create or Replace Match Recognize table/transient table |
| **Table/transient table to View** |  Drop table/transient table<br/> Create Match Recognize view |
| **Table to transient table or vice versa** |  Drop table/transient table<br/> Create or Replace Match Recognize table/transient table |

### Redeployment with no changes 

If the nodes are redeployed with no changes compared to previous deployment,then no stages are executed

### Match Recognize Tables Undeployment
If a Match Recognize Node of materialization type table/view/transient table are deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Match Recognize node in the target environment will be dropped.

This is executed in below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop table/view/transient table** | Removes the table or view from the environment |

## View-Qualify advanced deploy

The Coalesce View-Qualify advanced deploy UDN is a versatile node that allows you to develop and deploy a View in Snowflake with an added QUALIFY filter.

A view allows the result of a query to be accessed as if it were a table. Views serve a variety of purposes, including combining, segregating, and protecting data.In a SELECT statement, the QUALIFY clause filters the results of window functions.

[QUALIFY](https://docs.snowflake.com/en/sql-reference/constructs/qualify) does with window functions what HAVING does with aggregate functions and GROUP BY clauses.

### View Node Configuration

The View node type has two configuration groups:

* [Node Properties](#view-node-properties)
* [Options](#view-options)

![View-qualify NC](https://github.com/user-attachments/assets/6cf9f488-488f-4b0c-bf77-ab1b56f40b6c)

#### View Node Properties

| **Properties** | **Description** |
|----------|-------------|
| **Storage Location** | Storage Location where the WORK will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/> If FALSE the node will not be deployed or will be dropped during redeployment |

#### View Options

| **Options** | **Description** |
|---------|-------------|
| **QUALIFY filter** | Toggle: True/False<br/>**True**: Quaify filter on a window function is added to the view definition and the configs related to qualify filter are displayed <br/>**False**: A simple view is created |
| **Window functions** | A drop down with the list of window functions is visible.Window function to be used in QULAIFY filter predicate is chosen.  |
| **Window function column name**|The window fucntion is applied to this specific column.This is not required for window functions like 'ROW_NUMBER','RANK','DENSE_RANK','PERCENT_RANK','NTILE'|
| **Constant value-desired number of buckets**|Enabled whn the window function chosen is "NTILE"|
| **Partition By** | Toggle: True/False<br/>**True**: Lists the columns to be used for partitioning window function <br/>**False**: Partition by column drop down is invisible.Check [preferences](#preferences) for more info |
| **Order By** | Toggle: True/False<br/>**True**: Sort column and sort order drop down are visible and are required to form order by clause<br/>**False**: Sort column and sort order drop down are invisible.Check [preferences](#preferences) for more info |
| **Operator** | A drop with the list of comparison operators are listed down |
| **Compare return value of function with window column** | Toggle: True/False<br/>**True**: The return value of window function is compared with the column chosen for window function <br/>**False**: Compared with an expected value entered in the config below|
| **Expected value**| The expected value possibly an integer to compare the results of window function|

![View-Qialify options](https://github.com/user-attachments/assets/67a7d373-d437-4eaf-b30b-2deda610b4ba)

### Preferences

* PARTITION BY is optional.You can omit PARTITION BY if you want to treat the entire result set as one partition.
* ORDER BY is also optional.But it is required for ranking or cumulative functions where order matters (e.g., ROW_NUMBER(), RANK(), LAG(), LEAD())
* ORDER BY is optional for functions like AVG(), SUM(), etc., unless you want cumulative behavior.
  
### View Joins

Join conditions and other clauses like where, qualify can be specified in the join space next to mapping of columns in the Coalesce app.

> 📘 **Specify Group by Clauses**
>
> Best Practice is to specify group by clauses in this space if you are not opting for the group by all provided in OPTIONS config.

### View Deployment

#### View Initial Deployment

When deployed for the first time into an environment the View node will execute the Create View stage.

| **Stage** | **Description** |
|-----------|----------------|
| **Create View** | This will execute a CREATE OR REPLACE statement and create a View in the target environment |

#### View Redeployment

The subsequent deployment of View node with changes in node name or node location results in deleting the existing view and recreating the view.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop View** | Removes existing view |
| **Create View** | Creates new view with updated definition |

Changes in QUALIFY filter or view definition results in recreating the view.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create View** | Creates new view with updated definition |

#### View Undeployment

If a View Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the View in the target environment will be dropped.

This is executed in the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop View** | Removes the view from the environment |

## Code

### Date Table Code

| **Component** | **Link** |
|--------------|-----------|
| **Node definition** | [definition.yml](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Date-404/definition.yml)
| **Create Template** | [create.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Date-404/create.sql.j2) |
| **Run Template** | [run.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Date-404/run.sql.j2) |

### Pivot code

| **Component** | **Link** |
|--------------|-----------|
| **Node definition** | [definition.yml](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Pivot-409/definition.yml)|
| **Create Template** | [create.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Pivot-409/create.sql.j2) |
| **Run Template** | [run.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Pivot-409/run.sql.j2)

### Unpivot code

| **Component** | **Link** |
|--------------|-----------|
| **Node definition** | [definition.yml](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Unpivot-399/definition.yml)|
| **Create Template** | [create.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Unpivot-399/create.sql.j2) |
| **Run Template** | [run.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/Unpivot-399/run.sql.j2)

### Match Recognize code

| **Component** | **Link** |
|--------------|-----------|
| **Node definition** | [definition.yml](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/MatchRecognize-410/definition.yml)|
| **Create Template** | [create.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/MatchRecognize-410/create.sql.j2) |
| **Run Template** | [run.sql.j2](https://github.com/coalesceio/functional-node-types/blob/main/nodeTypes/MatchRecognize-410/run.sql.j2)

### View-Qualify advanced deploy

| **Component** | **Link** |
|--------------|-----------|
| **Node definition** | [definition.yml](https://github.com/coalesceio/Coalesce-Base-Node-Types---Advanced-Deploy/blob/main/nodeTypes/View-Qualifyadvanceddeploy-459/definition.yml) |
| **Create Template** | [create.sql.j2](https://github.com/coalesceio/Coalesce-Base-Node-Types---Advanced-Deploy/blob/main/nodeTypes/View-Qualifyadvanceddeploy-459/create.sql.j2) |

[Macros](https://github.com/coalesceio/functional-node-types/blob/main/macros/macro-1.yml)

