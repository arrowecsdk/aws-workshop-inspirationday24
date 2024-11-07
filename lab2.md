# Lab 2

## DynamoDB

Go to: __DynamoDB__

Click: __Create table__

Table name: __noteapp-table__

partition Key: __id__

Type: __String__

Table Settings:

Select: __Customize setttings__

Select: __DynamoDB Standard__

Select: __On-Demand__

Leave the rest as default

Click: __Create Table__

We need the __ARN__ (Amazon Resource Name)

Click on the new table: __noteapp-table__

Click: __Additional info__

The Amazon Resource Name (ARN) is in the last part of the section

It looks like this: __arn:aws:dynamodb:eu-north-1:50xxxxxx1854:table/noteapp-table__

Copy the __ARN__ and save it in a notepad

## IAM Policy and Role

Go to: __IAM__

In the left menu select: __Policies__

Click: __Create Policy__

Set Policy editor to: __JSON__

Copy below to the Policy editor:

And change the "Resource" to the ARN you saved before

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:YOUR_REGION:YOUR_ACCOUNT_ID:table/YOUR_TABLE_NAME"
        }
    ]
}

```

Click: __Next__

Name: __noteapp-table-Policy__

Click: __Create Policy__

In the left menu Select: __Roles__

Click: __Create Role__

Trusted entity type: __AWS Service__

Use Case: __EC2__

Click: __Next__

Permissions Policies:

Search for and select:

- noteapp-table-Policy
- AmazonSSMManagedInstanceCore

Click: __Next__

Rolename: __noteapp-ec2__

Click: __Create Role__

[Go to Lab 3](lab3.md)
