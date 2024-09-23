
## Dynamo DB is a one of the many non-relational databases other is DocumentDB, Keyspaces, Amazon Neptune.

## Key-value Database is how DynamoDB structures it data. It doesnt use rows and columns

## Step 1 ##
Create a DynamoDB table from scratch.
-Table Name
- Partition Key The partition key is part of the table's primary key. It is a hash value that is used to retrieve items from your table and allocate data across hosts for scalability and availability.
- Partition key values don't have to be unique. For example if "Color" is a partition key, items can share partition key values like "Blue", "Green", "Red" and more.
- Sort Key is optional,You can use a sort key as the second part of a table's primary key.
-  The sort key allows you to sort or search among all items sharing the same partition key.
- Customize settings for free tier use
- Turn Auto scaling off for costs savings.


### DynamoDB pfricing is based on RCUs and WCUs. The more consumed RCUs/WCUs , the more expenive cost. 

***Read/write capacity settings***

#### What are read capacity units?
Think of read capacity units (RCUs) as a way to measure how many engines DynamoDB is using to operate.
1 read capacity unit (RCU) = DynamoDB is using a single engine to run its read operations. This lets DynamoDB perform a max of 2 reads per second.
So if you decide you actually want DynamoDB to run 3 reads per second, 1 engine won't be enough! The capacity calculator updates RCUs to 2, so now there are two engines powering DynamoDB to run read operations.

#### What are write capacity units?
Write capacity units (WCUs) are just like read capacity units - they give your DynamoDB tables the engines to edit/update/delete data!
1 WCU = 1 item write/second.
Yup, that means it costs more to run write operations than read operations.

#### Create Table.

## Step 2 ##

Select Table, Explore Items, You can enter data (items) directly

Under Create item, you can add attributes. Each ITEM can have multiple attributes. 

***An attribute is a piece of data about an item in the DynamoDB table***  For example ITEM is student Name but attribute could be email, projects done.

![Screenshot (23)](https://github.com/user-attachments/assets/a1e3d988-3fe1-48f6-9cfc-6dc1fdc55214)


You see Partition key (StudentName) and  Attributes.
Under the Partition Key is the ***created ITEM*** and  under the ***Attributes*** is the value of attributes

So Data is organized in DynamoDB using items and attributes.

## Step 3 ##

### Create DynamoDB tables with AWS CloudShell


<img width="1157" alt="architecture-diagram3" src="https://github.com/user-attachments/assets/ddd08672-26e9-4e87-b59c-d5def49a38e3">

AWS CloudShell is shell in your AWS Management Console, which means it's a space for you to run code! The awesome thing about AWS CloudShell is that it already has AWS CLI pre-installed.

***Read AWS DynamoDB and AWS CLoudshell docs to get bash commands to create Resources***
```
aws dynamodb create-table \
    --table-name ContentCatalog \
    --attribute-definitions \
        AttributeName=Id,AttributeType=N \
    --key-schema \
        AttributeName=Id,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Forum \
    --attribute-definitions \
        AttributeName=Name,AttributeType=S \
    --key-schema \
        AttributeName=Name,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Post \
    --attribute-definitions \
        AttributeName=ForumName,AttributeType=S \
        AttributeName=Subject,AttributeType=S \
    --key-schema \
        AttributeName=ForumName,KeyType=HASH \
        AttributeName=Subject,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Comment \
    --attribute-definitions \
        AttributeName=Id,AttributeType=S \
        AttributeName=CommentDateTime,AttributeType=S \
    --key-schema \
        AttributeName=Id,KeyType=HASH \
        AttributeName=CommentDateTime,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"

```


Let's confirm those tables were actually created. Run these wait commands, and wait until they all run and end:
```
aws dynamodb wait table-exists --table-name ContentCatalog
aws dynamodb wait table-exists --table-name Forum
aws dynamodb wait table-exists --table-name Post
aws dynamodb wait table-exists --table-name Comment

```
***What are wait commands?***
When you run a wait command, you're telling your terminal to keep waiting until a condition is finally met. In our case, we're saying "keep waiting - don't finish running this command until this table has been created."

Wait commands are helpful for making sure necessary resources have been created before you move on. Otherwise, future commands that depend on your resources would automatically fail!

You should see and verify Tables created by checking DynamoDB.
![Screenshot (24)](https://github.com/user-attachments/assets/2b36a22b-63be-48fc-bf99-020913029543)


## Step 4 ##
### Load Data into Your Tables
In this step, you're going to:
1. Load some data into DynamoDB tables.
2. View and update your loaded data.
Tables created but are empty. Tables need needs Items and Attributes.
<img width="966" alt="load data architecture-diagram" src="https://github.com/user-attachments/assets/ecb4b2e6-dd10-47a7-a4d1-d350f3128979">

In CloudShell terminal

```
curl -O https://storage.googleapis.com/nextwork_course_resources/courses/aws/AWS%20Project%20People%20projects/Project%3A%20Query%20Data%20with%20DynamoDB/nextworksampledata.zip

unzip nextworksampledata.zip

cd nextworksampledata

```
run ***ls command*** to confirm that all files are now inside your CloudShell environment:

Run ***cat Forum.json**

![Screenshot (25)](https://github.com/user-attachments/assets/145db7f3-c7a1-4865-bd52-ad9cf193fbfb)

![Screenshot (26)](https://github.com/user-attachments/assets/dd12c2bb-a052-41b2-bb52-3d58830a2e9d)

Load the data of all four files into DynamoDB using AWS CLI's 
***batch-write-item*** command:
 
```
aws dynamodb batch-write-item --request-items file://ContentCatalog.json

aws dynamodb batch-write-item --request-items file://Forum.json

aws dynamodb batch-write-item --request-items file://Post.json

aws dynamodb batch-write-item --request-items file://Comment.json

```

💡 What does this command  do?
The ***aws dynamodb batch-write-item*** command is used to load or insert multiple items into DynamoDB tables!

 ***--request-items***tells DynamoDB that the items are currently stored inside a file that it'll need to retrieve from.

 ***file://*** then tells DynamoDB that the file is stored locally in the CloudShell environment, with the name FILENAME.json.

💡 How does DynamoDB know which table to store which data?
Each .json file you upload tells DynamoDB which table the items should go to!


💡 What's an Unprocessed Item?
Unprocessed items are records that weren't written to your database! If you see an unprocessed item, an error happened while loading your data into DynamoDB.


#### View and update your loaded data ####

- Head back to the DynamoDB console.
- Select Tables from the left hand navigation panel.
- Pick the ContentCatalog table. ( Refresh/reload if you don't see anything first)
- Select Explore table items on the top right.
- Wooooohoo! Your items are now on display


Click Add new attribute, select String from the dropdown.
Name the new attribute ***StudentsComplete***
For the value, enter ***Nikko***
![Screenshot (28)](https://github.com/user-attachments/assets/24cb5839-480f-4d29-b8ce-f647547a13ec)
![Screenshot (29)](https://github.com/user-attachments/assets/7b22dcd1-fae6-4db5-9c7a-3771c92cf2df)
![Screenshot (30)](https://github.com/user-attachments/assets/7259f8ca-bf5f-4b7d-b580-e31c4605806e)
![Screenshot (31)](https://github.com/user-attachments/assets/de752b90-ca3e-415f-b45d-5d97179bc8c6)


#### Difference between DynamoDB and a Relational DB ####
###### What's the difference between this and a relational database?######
Relational databases would need each row to have the same number of columns. So if you added StudentsComplete as a new column in a relational database, every item in that database would need to have a StudentsComplete value too, even if it doesn't apply.

This has huge impacts on a DynamoDB vs a relational database's flexibility and speed!

**Flexibility** - every item having their own unique set of attributes is a huge advantage when items in a table could look different from each other. For example, e-commerce sites and shopping carts need to store different types of products with different attributes in the same place.
**Speed** - DynamoDB tables can use partition keys to split up a table and quickly find the items they're looking for. Relational databases have to scan through the entire table to find data, which can slow down performance.

##### When would someone pick relational databases over non-relational?#####
Relational databases use SQL, which makes handling complex queries a lot more straightforward!

The strictness of a relational database's schema also means data is kept precise, accurate and consistent, which can be helpful for situations where the quality of data is a top priority (e.g. healthcare systems often opt for relational databases to keep patient records).


## Step 5 ##
### Delete Your Resources###
You can delete each table from management console one by one or  try delete your tables using AWS CloudShell!
This command will delete all of their items too

```
aws dynamodb delete-table --table-name Comment
aws dynamodb delete-table --table-name Forum
aws dynamodb delete-table --table-name ContentCatalog
aws dynamodb delete-table --table-name Post
aws dynamodb delete-table --table-name NextWorkStudents

```
