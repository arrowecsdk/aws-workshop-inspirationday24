# Prepare

````bash
#/bin/sh
# Usage
#
# Create users for AWS Workshop
#
# using aws cli
#
# Author: Jesper Berth, jesper.berth@arrow.com - november 2024

EMAILUSER=me
EMAILDOMAIN=@example.com

IDENTITY_STORE_ID=$(aws sso-admin list-instances --query 'Instances[0].IdentityStoreId')

IDENTITY_STORE_ID="${IDENTITY_STORE_ID//\"}"

printf "#############################\n"
printf "# Create accounts and users #\n"
printf "#############################\n\n"
printf "How many users?\n"
read NUMBERUSERS

create_users () {
    printf "Creating $1 new users\n"
    for (( i=1 ; i<=$1 ; i++ ));
    do
        USER=user$i
        printf "Create AWS Account for - $USER\n\n"
        EMAIL="$EMAILUSER+$USER$EMAILDOMAIN"
        printf "With root email: $EMAIL\n\n"
        aws organizations create-account --email $EMAIL --account-name "$USER Account"
        sleep 20
        aws identitystore create-user --identity-store-id $IDENTITY_STORE_ID --user-name $USER --emails '[{ "Value": "'${EMAIL}'", "Primary": true }]' --display-name $USER --name '{"GivenName": "User","FamilyName": "Workshop"}'
        sleep 10
    done
}

create_users $NUMBERUSERS
```


```bash
aws organizations create-account --email $EMAIL --account-name "$USER Account"

aws organizations describe-create-account-status --create-account-request-id "car-5be85ab4f31a4d22880c766d0452c918"

EMAIL=me+user2@example.com

aws identitystore create-user --identity-store-id $IDENTITY_STORE_ID --user-name "user2" --emails '[{ "Value": "'${EMAIL}'", "Primary": true }]' --display-name "User2" --name '{"GivenName": "User","FamilyName": "Two"}'

USER_ID=$(aws identitystore list-users \
    --identity-store-id $IDENTITY_STORE_ID \
    --filters '[{"AttributePath": "UserName","AttributeValue": "user2"}]' \
    --query 'Users[0].UserId' \
    --output text)


```

