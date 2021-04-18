Note that the production S3 code repo is different from the dev environment. These instructions are for the prod environment. They won't work for dev until its S3 code repo is updated.

## 1. Acquire ChatAppServer jar

This is the Java/Kotlin server-side application which communicates via websocket to handle/send chat commands/events, and reads/writes chat data to DynamoDB tables.

In the [ChatApp_server repo](https://github.com/kingevents/ChatApp_server) there are two projects of interest, which are built using Maven.

- First, run `mvn install` in the `chatlibrary` directory.
- Then, run `mvn install` in the `chatappserver` directory. This maven project depends on `chatlibrary`.

The .jar should now be built in:

`chatappserver/target/chatappserver-[version]-jar-with-dependencies.jar`

Upload this .jar file to an AWS S3 bucket, and note its <i>bucket name & file name</i>. We will need to refer to it later.

## 2. Acquire AWS Cloudformation yamls

These files will be used to create all the AWS infrastructure needed to run the chat server.

In the `dev-ops` repo, acquire the following files:

- `chat.gatewayandlambda.yaml` defines the API gateway and the lambda function
- `chat.dynamodb.yaml` - defines the dynamoDBs and their indexes.
- `chat.masterstack.prod.yaml` - connects the previous 2 files and defines all of their input paramters

upload `chat.gatewayandlambda.yaml` and `chat.dynamodb.yaml` to an AWS S3.

In the AWS Cloudformation webpage, click `Create Stack`. Upload `chat.masterstack.prod.yaml` as a new template. Enter the following parameters when asked:

- All Table/Index names can use their defaults. These are here so the lambda & tables use the same names. 
- Many of the resources names are derived from these parameters and do not use the parameters as is. Those resources are flagged with a '*'
- CodeRepoBucket: the name of the bucket that contains the stacks and lambdas.
- CodeRepoLambdaLocation: the folder that contains the lambdas.
- CodeRepoCloudFormationStackLocation: the folder that contains the stacks.
- ChatServiceGatewayAndLambdaScriptName: the chat service lambda and gateway cloudformation stack, e.g. chat.gatewayandlambda.yaml
- ChatServiceLambdaFile: chat service lambda jar file name *
- ChatServiceLambdaVersion: chat service lambda jar file version *
- ChatServiceCognitoRegion: region that houses the cognito service (when the event/site has an authenticated chat)
- ChatServiceCognitoPoolId: id of the cognito service (when the event/site has an authenticated chat)
- ChatServiceCognitoClientAppId: client app id for the event web site (when the event/site has an authenticated chat)
- ChatServiceDBDynamoDbScriptName: the name of the cloudformation stack that creates the chat db tables, e.g. `chat.dynamodb.yaml`
- ChatServiceDBConversationTableName: name of the table that stores the event's chat conversation list *
- ChatServiceDBConversationParticipantTableName: name of the table that stores the event's chat conversation participant list *
- ChatServiceDBMessageTableName: name of the table that stores the event's chat message *
- ChatServiceDBUserTableName: name of the table that stores the event's chat participants *
- ChatServiceDBConversationConversationTypeIndexName - conversation table secondary index *
- ChatServiceDBConversationParticipantUserIdIndexName - conversation participant table secondary index *
- ChatServiceDBConversationParticipantConversationIdIndexName - conversation participant table secondary index *
- ChatServiceDBMessageConversationIdCreatedIndexName - conversation table secondary index *

- Read/WriteCapacityUnits: capacity of the dynamodbs. I haven't tried anything but 5 

This creates all AWS resources needed for chat.

## 3. Add Conversations to the DBs

The `Conversation` table starts out empty, we need to add conversations to it on the AWS DynamoDB webpage. 

- First, we need a user. In the newly created `User` table, click `Create Item` and use the following input (in Text mode):
```
{
  "connectionId": null,
  "created": "1614722594",
  "id": "83003df6-3eec-4bcc-81a2-c90ccc790414",
  "lastConnected": "1614748958",
  "name": "Admin User"
}
```
you can use https://www.uuidgenerator.net/ for a random id, and https://www.epochconverter.com/ to get the current created/lastConnected epoch times. Note that the times are strings.

Then, add any conversations you'd like to the `Conversation` table. There will likely be a conversation for the main stage chats (one per?) and for each lounge. Here's an example main stage conversation. The other conversations should vary by name (use the room name?) and each must have it's own id.
```
{
  "conversationType": "OpenToAll",
  "createdBy": "83003df6-3eec-4bcc-81a2-c90ccc790414",
  "id": "4251d635-3e68-46eb-a3bc-5508cc5abfe5",
  "isActive": "true",
  "name": "MAINSTAGE CHAT"
}
```
where `createdBy` is the id of the admin user.

## 4. Client Setup

First, we'll need the url of the websocket server. On the AWS API Gateway webpage, find the API gateway that was made in Step 2. Navigate to `Stages`, `Prod`, note the `WebSocket URL`. It should look something like this:

`wss://gt5d1e63vj.execute-api.us-east-2.amazonaws.com/Prod`

In the client's `config.json`, on any pages that wish to use Chat, use the following chat parameter:

```
"chat": {
  "type": "king-events",
  "kingEventsChatParameters": {
    "url": "[THE WEBSOCKET URL FROM ABOVE]",
    "showConversationMembers": true,
    "allowNameChange": false,
    "onlyShowConversations": [],
    "hideConversations": []
  }
},
```
`onlyShowConversations` is an array of conversation ids that you wish to exclusively show (e.g. mainstage conversation).
`hideConversations` is an array of conversation ids that you wish to hide (e.g. mainstage conversation in the lounge).

## Done!
