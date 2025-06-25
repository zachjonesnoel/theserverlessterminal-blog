---
title: "DynamoDB with PartiQL (Part - 2)"
datePublished: Sun Jul 25 2021 17:22:47 GMT+0000 (Coordinated Universal Time)
cuid: clduer4ji00endonvgcvv4j17
slug: dynamodb-with-partiql-part-2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784378863/9dd6f09a-0d98-4d55-a769-820e02a05027.jpeg

---

In the previous post, an overview of DynamoDB with PartiQL was provided. In this post, you will be understanding how PartiQL works with NodeJS code with the DynamoDB SDK API `executeStatement`.
{% link https://dev.to/awscommunity-asean/dynamodb-with-partiql-2693 %}

#### Key takeaways from this blog post 
+ Understanding of DynamoDB SDK for PartiQL execution.
+ Building and structuring parameters.

#### Understanding of DynamoDB SDK for PartiQL execution
[DynamoDB SDK provides support for PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html) with the APIs - `executeStatement`, `executeTransaction` and for batch statements `batchExecuteStatement`. 

`executeStatement` is used to execute read operation and singleton write operation for the data in DynamoDB. 

```JavaScript
let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'TEAM'`,
    }
let response = await dynamodb.executeStatement(partiqlStmt).promise()
return response
```

`executeTransaction` is used to execute transaction reads or writes and since it is based on transactional, we can execute multiple statements also.

```JavaScript
let partiqlStmt = [{
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'MATCH'`,
    }]
let response = await dynamodb.executeTransaction(partiqlStmt).promise()
return response
```

`batchExecuteStatement` is `executeStatement` with the feasibility of executing multiple statements.

```JavaScript
    let partiqlInsertParams = {
        Statements: []
    }
    for (let team of event.teams) {
        let teamParams = {
            pk: "TEAM",
            sk: team.name + "#" + team.group + "#" + team.ranking,
            display_name: team.name,
            team_group: team.group,
            ranking: team.ranking,
            matches_played: team.matches_played,
            matches_won: team.matches_won,
            matches_drew: team.matches_drew,
            matches_lost: team.matches_lost,
            goals_for: team.goals_for,
            goals_against: team.goals_against,
            goals_difference: team.goals_difference,
            team_points: team.team_points
        }
        let partiqlStmt = {
            Statement: `INSERT INTO "testing-partiql" VALUE "{'pk':'${teamParams.pk}','sk':'${teamParams.sk}','display_name':'${teamParams.display_name}','team_group':'${teamParams.team_group}','ranking':${teamParams.ranking},'matches_played':${teamParams.matches_played},'matches_won':${teamParams.matches_won},'matches_drew':${teamParams.matches_drew},'matches_lost':${teamParams.matches_lost},'goals_for':${teamParams.goals_for},'goals_against':${teamParams.goals_against},'goals_difference':${teamParams.goals_difference},'team_points':${teamParams.team_points}}"`,
        }
        partiqlInsertParams.Statements.push(partiqlStmt)
    }
    let response = await dynamodb.batchExecuteStatement(partiqlInsertParams).promise()
    return response
```

#### Building and structuring parameters
A SQL based query with a programmatic approach share a lot of similarities with AWS DynamoDB PartiQL statement/transaction execution. Every query executed with `executeStatement`, `executeTransaction` and `batchExecuteStatement` have a structure which has to be followed. 

[JSON structure](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB.html#executeStatement-property)
```json
{
      Statement: 'STRING_VALUE', /* required */
      Parameters: [
        { /* AttributeValue */
          B: Buffer.from('...') || 'STRING_VALUE' /* Strings will be Base-64 encoded on your behalf */,
          BOOL: true || false,
          BS: [
            Buffer.from('...') || 'STRING_VALUE' /* Strings will be Base-64 encoded on your behalf */,
            /* more items */
          ],
          L: [
            /* recursive AttributeValue */,
            /* more items */
          ],
          M: {
            '<AttributeName>': /* recursive AttributeValue */,
            /* '<AttributeName>': ... */
          },
          N: 'STRING_VALUE',
          NS: [
            'STRING_VALUE',
            /* more items */
          ],
          NULL: true || false,
          S: 'STRING_VALUE',
          SS: [
            'STRING_VALUE',
            /* more items */
          ]
        },
        /* more items */
      ]
    }
```
This comprises of the `Statement` itself which is the SQL statement. `Parameters` for all the input parameters based on the different DynamoDB supported datatypes - String, Boolean, String sets, Maps, Lists, Number, Number strings and null. For any the statements we can include the parameters in the statement but also able be to send the parameters in `Parameters` value. DynamoDB supports the concept of pagination of values with `NextToken` which is returned in the query if the statement has more data to be returned. In case of PartiQL, the limit is set in the statement otherwise by default with the limits of DynamoDB the data are returned. 

PartiQL statement without parameters.
```JavaScript
    let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" `,
    }
```
PartiQL statement with parameters, and the parameters been set in Statement.
```JavaScript
    let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'TEAM' AND contains("display_name",'${params.id}')`,
    }
```
PartiQL statement with parameters, and the parameters been set in Parameters attribute.
```JavaScript
    let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'TEAM' AND contains("display_name",'?')`,.
        Parameters: [{
                  "S":  params.id
             }
        ]
    }
```
This variants of statement objects are sent in `executeStatement`. For `executeTransaction` and `batchExecuteStatement` it would be an array of these variants of statement objects.

#### Implementation
For the implementation of PartiQL based `SELECT` statements, from the previous blog post, I've continued to develop queries for -
+ Getting all the teams for Copa America
```JavaScript
let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'TEAM'`,
    }
let response = await dynamodb.executeStatement(partiqlStmt).promise()
return response
```
Response 
![all the teams for Copa America](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784372878/ad9cd3f9-e34f-4e63-a339-fb4fbf81898b.png)
+ Getting all the matches for Copa America
```JavaScript
let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'MATCH'`,
    }
let response = await dynamodb.executeStatement(partiqlStmt).promise()
return response
```
Response 
![all the matches for Copa America](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784374610/99817b95-6642-4fac-8c82-78e3f44dcf75.png)
+ Getting team info
```JavaScript
let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'TEAM' AND contains("display_name",'${params.id}')`,
    }
let response = await dynamodb.executeStatement(partiqlStmt).promise()
return response
```
Response 
![team info](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784376225/5e39726e-da5e-4e15-a705-1cb468cba71d.png)
+ Getting all the matches played by a team
```JavaScript
let partiqlStmt = {
        Statement: `SELECT * FROM "copa-america" WHERE "pk" = 'TEAM' AND contains("display_name",'${params.id}')`,
    }
let response = await dynamodb.executeStatement(partiqlStmt).promise()
return response
```
Response 
![all the matches played by a team](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784377784/026c79f1-9a70-45e1-bd15-41c703cff590.png)

This has been implemented with AWS SAM application with NodeJS, the sample code is available on GitHub.
{% github zachjonesnoel/copa-america-partiql-demo/ %}