# Chat overview



# Message sequences


## Connect

Adds user to list of active users.
Sends user a list of conversations in which the user can participate.
Sends all active users (including the one just connected) a new user message.

![connect](Chat/plantumldiagrams/artefacts/connect.png)

## Disconnect

![disconnect](Chat/plantumldiagrams/artefacts/disconnect.png)

## Create Conversation

Currently only supports the creation of a Direct Message conversation

![create conversation](Chat/plantumldiagrams/artefacts/createconversation.png)

## Invite To Conversation

Not needed to current conversation types

![invite to  conversation](Chat/plantumldiagrams/artefacts/invitetoconversation.png)

## Leave Conversation

Not needed for current conversation types

![leave conversation](Chat/plantumldiagrams/artefacts/leaveconversation.png)

## Remove Conversation

Not needed for current conversation types

![remove conversation](Chat/plantumldiagrams/artefacts/deleteconversation.png)

## Add Message to Conversation

Used for all conversations except reply conversations

Send a new message notification to all participants of the conversation (including the one that sent the message)

![add message to conversation](Chat/plantumldiagrams/artefacts/sendmessage.png)

## Add Message to Reply Conversation (Reply to Message)

Not needed for current conversation types

The conversation is automatically created, if it doesn't exist

Send a new message notification to all participants of the conversation (including the one that sent the message)

![reply to message](Chat/plantumldiagrams/artefacts/sendreplymessage.png)

## Get Messages Since

![get messages since](Chat/plantumldiagrams/artefacts/getconversationmessagesaftercomment.png)

## Get Conversation Participants

Not implemented - not need for current conversation types


## Get Prior Messages

Not implemented - will need soon



