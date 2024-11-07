# Lab 2

## DynamoDB

Go to: DynamoDB

Click: Create table

Table name: noteapp-table

partition Key: id

Type: String

Table Settings:

Select: Customize setttings

Select: DynamoDB Standard

Select: On-Demand

Leave the rest as default

Click: Create Table

We need the ARN (Amazon Resource Name)

Click on the new table: noteapp-table

Click: Additional info

The Amazon Resource Name (ARN) is in the last part of the section

Copy the ARN and save it in a notepad

## IAM Policy and Role

Go to: IAM

In the left menu select: Policies

Click: Create Policy

Set Policy editor to: JSON

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

Click: Next

Name: noteapp-table-Policy

Click: Create Policy

In the left menu Select: Roles

Click: Create Role

Trusted entity type: AWS Service

Use Case: EC2

Click: Next

Permissions Policies:

Search for and select:

- noteapp-table-Policy
- AmazonSSMManagedInstanceCore

Click: Next

Rolename: noteapp-ec2

Click: Create Role

[Go to Lab 3](lab3.md)
