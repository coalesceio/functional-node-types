## Functional Node Types

The Coalesce Functional Node Types Package includes:

* [Pivot](#Pivot)
* [Date Dimension](#date-table)
* [Code](#code)

---

## Date Dimension

The Coalesce Date Dimension Table provides a comprehensive breakdown of date-related attributes, enabling efficient handling of date operations across various
use cases. The table typically includes columns such as day, month, year, day of the week, week of the year, quarter, and flags like day is weekday
or weekend. Additional columns like fiscal year, fiscal quarter, holiday indicators can also be included, depending on the requirements.

### Date Dimension Node Configuration

The Date Dimension node type has two configuration groups:

* [Node Properties](#date-table-node-properties)
* [Options](#date-table-options)

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
| **Rename Table\| Alter Column \| Delete Column \| Add Column \| Edit table description** | Alter table statement is executed to perform the alter operation |

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

Pivot has two configuration groups: 

* [Node Properties](#pivot-node-properties)
* [General Options](#pivot-general-options)
* [Pivot Options](#pivot-options)

  ![image](https://github.com/user-attachments/assets/edd67d5d-7216-429a-a292-2fe4980d1a9e)


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
| **Infer structure of Pivot table** | Toggle: True/False <br/> True,it is the first run and the pivot table structure is yet to be determined</br>False,when the pivot table is created and generated columns have been Re-synced in Coalesce|
| **Pivot column**|Pivot column(Dropdown) </br>Pivot column(textbox)The column from the source table or subquery that will be aggregated and turned into new columns.|
| **Single value column** |Toggle: True Determines which if analysis of single or multiple value columns to be added.Value column is the column from the source table or subquery that contains the values from which column names will be generated. |
|**Value Column**|-Value Column(Dropdown) </br> -Value Column(textbox) </br> Values you want to populate in the new columns.|
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
| **Pivot column**|Pivot column(Dropdown) </br> Pivot column(textbox)</br> The column from the source table or subquery that will be aggregated and turned into new columns.|
|**Pivot operation on same column values**|Toggle:True/False </br>- True If pivot is to applied to same pivot column values for multiple value columns</br>- False If pivot is to applied to differnt pivot column values for each value column|
| **Single value column** |Toggle:False Determines which if analysis of single or multiple value columns to be done.Value column is the column from the source table or subquery that contains the values from which column names will be generated. |
|**Value Column**|-Value Column(Dropdown) </br> -Value Column(textbox)</br> Values you want to populate in the new columns.</br>-Aggregate Functions</br>Aggregation you want to apply, like AVG, COUNT, MAX, MIN, and SUM.</br>-Column Values </br> Enabled if the Pivot operation on same column values is false|
|**Filter Column Values(comma separated list of column values-Ex 'Q1','Q2')**|Specified list of column values for the pivot column|
|**Default value for NULL**|Replace all NULL values in the pivot result with the specified default value. The default value can be any scalar expression that does not depend on the pivot and aggregation column|

### Pivot node Usage

* Add a Pivot node on top of source node
* Add the pivot columns,value columns ,aggregation operation from config
* When you choose the pivot and value dropdown,ensure that the textbox alongside the dropdown is entered with Column name.This textBox information is required once the pivot table structure is synced into Coalesce.
* The toggle 'Infer Structure of Pivot Data' is required to be true when the node is created for the first time.
* The toggle 'Single value column' is set to false, if you want a multi-dimensional pivot
* Once the pivot table is created,the 'Re-Sync Columns' can be used to sync the structure of pivot table into Coalesce mapping grid.
* For further pivot operations,keep the 'Infer Structure of Pivot Data' set to false

### Pivot Deployment
### Pivot Initial Deployment
When deployed for the first time into an environment the Pivot node of materialization type table or view or transient table will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Pivot Table/transient table/view** | This will execute a CREATE OR REPLACE statement and create a pivot table in the target environment |

#### Pivot Table Redeployment

When the PIVOT node is redeployed with any changes in table or config changes result in re-creating the node

The below stage is executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Pivot Table/transient table/view** | This will execute a CREATE OR REPLACE statement and create a pivot table in the target environment |

#### Pivot Table Deploy Drop and Recreate Work View/Table/Transient Table

| **Change** | **Stages Executed** |
|------------|-------------------|
| **View to table/transient table** |  Drop view <br/> Create or Replace Pivot table/transient table |
| **Table/transient table to View** |  Drop table/transient table<br/> Create Pivot view |
| **Table to transient table or vice versa** |  Drop table/transient table<br/> Create or Replace Pivot table/transient table |

### Pivot Tables Undeployment
If a Pivot Node of materialization type table/view/transient table are deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Pivot node in the target environment will be dropped.

This is executed in below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop table/view/transient table** | Removes the table or view from the environment |

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
