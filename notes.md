
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


