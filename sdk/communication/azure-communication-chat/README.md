# Azure Communication Chat client library for Android

Azure Communication Chat contains the APIs used in chat applications for Azure Communication Services.

[Source code][source] | [Package (Maven)][package] | [API reference documentation][api_documentation]
| [Product documentation][product_docs]

## Getting started

### Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) version 8 or above.
- [Apache Maven](https://maven.apache.org/download.cgi).
- A deployed Communication Services resource. You can use the [Azure Portal](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp) or the [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.communication/new-azcommunicationservice) to set it up.

### Versions available
The current version of this library is **2.2.0-beta.1**.

### Include the package
To install the Azure Communication Chat libraries for Android, add them as dependencies within your
[Gradle](#add-a-dependency-with-gradle) or
[Maven](#add-a-dependency-with-maven) build scripts.

#### Add a dependency with Gradle
To import the library into your project using the [Gradle](https://gradle.org/) build system, follow the instructions in [Add build dependencies](https://developer.android.com/studio/build/dependencies):

Add an `implementation` configuration to the `dependencies` block of your app's `build.gradle` or `build.gradle.kts` file, specifying the library's name and the version you wish to use:

```gradle
// build.gradle
dependencies {
    ...
    implementation "com.azure.android:azure-communication-chat:2.2.0-beta.1"
}

// build.gradle.kts
dependencies {
    ...
    implementation("com.azure.android:azure-communication-chat:2.2.0-beta.1")
}
```

#### Add a dependency with Maven
To import the library into your project using the [Maven](https://maven.apache.org/) build system, add it to the `dependencies` section of your app's `pom.xml` file, specifying its artifact ID and the version you wish to use:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>2.2.0-beta.1</version>
</dependency>
```

## Key concepts

A chat conversation is represented by a chat thread. Each user in the chat thread is called a participant. Participants can chat with one another privately in a 1:1 chat or huddle up in a 1:N group chat.

Once you initialized a `ChatClient` and a `ChatThreadClient` class, you can do the following chat operations:

### Create, get, list, update, and delete chat threads

### Send, get, list, update, and delete chat messages

### Get, add, and remove participants

### Send and get read receipts

## Contributing

This project welcomes contributions and suggestions. Most contributions require you to agree to a [Contributor License Agreement (CLA)][cla] declaring that you have the right to, and actually do, grant us the rights to use your contribution.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct][coc]. For more information see the [Code of Conduct FAQ][coc_faq] or contact [opencode@microsoft.com][coc_contact] with any additional questions or comments.

### Set Azure Communication Resource endpoint after it is created

endpoint = "https://*Azure-Communication-Resource-Name*.communications.azure.com"

### Request a User Access Token

User access tokens enable you to build client applications that directly authenticate to Azure Communication Services.
You generate these tokens on your server, pass them back to a client device, and then use them to initialize the Communication Services SDKs.

Learn how to generate user access tokens from [User Access Tokens](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java#issue-user-access-tokens)

## Examples

The following sections provide several code snippets covering some of the most common tasks, including:

- [Chat Thread Operations](#thread-operations)
- [Chat Message Operations](#message-operations)
- [Chat Thread Member Operations](#thread-member-operations)
- [Read Receipt Operations](#read-receipt-operations)
- [Typing Notification Operations](#typing-notification-operations)

## Create the chat client

To create a chat client, you will use the Communications Service endpoint and the access token that was generated as part of pre-requisite steps. User access tokens enable you to build client applications that directly authenticate to Azure Communication Services. Once you generate these tokens on your server, pass them back to a client device. You need to use the CommunicationTokenCredential class from the Common SDK to pass the token to your chat client.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L41-L51 -->
```Java
String endpoint = "https://<RESOURCE_NAME>.communcationservices.azure.com";

// Your user access token retrieved from your trusted service
String token = "SECRET";

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(token))
ChatClient chatClient = builder.buildClient();
```

### Chat Thread Operations

#### Create a chat thread

Use the `createChatThread` method to create a chat thread.
`createChatThreadOptions` is used to describe the thread request, an example is shown in the code snippet below.

- Use `topic` to give a thread topic;
- Use `participants` to list the thread participants to be added to the thread;

`CreateChatThreadResult` is the response returned from creating a chat thread.
It contains a `getChatThread()` method which returns the `ChatThread` object that can be used to get the thread client from which you can get the `ChatThreadClient` for performing operations on the created thread: add participants, send message, etc.
The `ChatThread` object also contains the `getId()` method which retrieves the unique ID of the thread.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L65-L82 -->
```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user2)
    .setDisplayName("Participant Display Name 2");

participants.add(firstParticipant);
participants.add(secondParticipant);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setParticipants(participants);
CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThreadProperties().getId();
```

#### Delete a thread

Use `deleteChatThread` method to delete a chat thread
`chatThreadId` is the unique ID of the chat thread.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L101-L102 -->
```Java
String chatThreadId = "Id";
chatClient.deleteChatThread(chatThreadId);
```

#### Get a chat thread client

The `getChatThreadClient` method returns a thread client for a thread that already exists. It can be used for performing operations on the created thread: add participants, send message, etc.
`chatThreadId` is the unique ID of the existing chat thread.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L113-L114 -->
```Java
String chatThreadId = "Id";
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

#### Get properties of a chat thread

The `getProperties` method retrieves properties of a thread from the service.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L92-L92 -->
```Java
ChatThreadProperties chatThreadProperties = chatThreadClient.getProperties();
```

#### Update a chat thread topic

Use `updateTopic` method to update a thread's topic
`topic` is used to hold the new topic of the thread.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L125-L125 -->
```Java
chatThreadClient.updateTopic("New Topic");
```

### Chat Message Operations

#### Send a chat message

Use the `sendMessage` method to sends a chat message to a chat thread identified by chatThreadId.
`sendChatMessageOptions` is used to describe the chat message request, an example is shown in the code snippet below.

- Use `content` to provide the chat message content;
- Use `type` to specify the chat message type;
- Use `senderDisplayName` to specify the display name of the sender;
- Use `metadata` to specify the message metadata;

A `SendChatMessageResult` response returned from sending a chat message, it contains an `id`, which is the unique ID of the message.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L136-L142 -->
```Java

SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setSenderDisplayName("Sender Display Name");


String chatMessageId = chatThreadClient.sendMessage(sendChatMessageOptions).getId();
```

#### Get a chat message

The `getMessage` method retrieves a chat message from the service.
`chatMessageId` is the unique ID of the chat message.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L151-L152 -->
```Java
String chatMessageId = "Id";
ChatMessage chatMessage = chatThreadClient.getMessage(chatMessageId);
```

#### Get chat messages

You can retrieve chat messages using the `listMessages` method on the chat thread client at specified intervals (polling).

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L161-L164 -->
```Java
PagedIterable<ChatMessage> chatMessages = chatThreadClient.listMessages();
chatMessages.forEach(chatMessage -> {
    // You code to handle single chatMessage
});
```

`listMessages` returns the latest version of the message, including any edits or deletes that happened to the message using `.editMessage()` and `.deleteMessage()`.

For deleted messages, `chatMessage.getDeletedOn()` returns a datetime value indicating when that message was deleted.

For edited messages, `chatMessage.getEditedOn()` returns a datetime indicating when the message was edited.

The original time of message creation can be accessed using `chatMessage.getCreatedOn()`, and it can be used for ordering the messages.

listMessages returns different types of messages which can be identified by `chatMessage.getType()`. These types are:

- `text`: Regular chat message sent by a thread participant.

- `html`: HTML chat message sent by a thread participant.

- `topicUpdated`: System message that indicates the topic has been updated.

- `participantAdded`: System message that indicates one or more participants have been added to the chat thread.

- `participantRemoved`: System message that indicates a participant has been removed from the chat thread.

For more details, see [Message Types](https://docs.microsoft.com/azure/communication-services/concepts/chat/concepts#message-types).

#### Update a chat message

Use `updateMessage` to update a chat message identified by chatThreadId and messageId.
`chatMessageId` is the unique ID of the chat message.
`updateChatMessageOptions` is used to describe the request of a chat message update, an example is shown in the code snippet below.

- Use `content` to provide a new chat message content;

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L173-L177 -->
```Java
String chatMessageId = "Id";
UpdateChatMessageOptions updateChatMessageOptions = new UpdateChatMessageOptions()
    .setContent("Updated message content");

chatThreadClient.updateMessage(chatMessageId, updateChatMessageOptions);
```

#### Delete a chat message

Use `updateMessage` to update a chat message identified by chatThreadId and chatMessageId.
`chatMessageId` is the unique ID of the chat message.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L186-L187 -->
```Java
String chatMessageId = "Id";
chatThreadClient.deleteMessage(chatMessageId);
```

### Chat Thread Participant Operations

#### List chat participants

Use `listParticipants` to retrieve a paged collection containing the participants of the chat thread identified by chatThreadId.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L196-L199 -->
```Java
PagedIterable<ChatParticipant> chatParticipants = chatThreadClient.listParticipants();
chatParticipants.forEach(chatParticipant -> {
    // You code to handle single chatParticipant
});
```

#### Add participants

Use `addParticipants` method to add participants to the thread identified by threadId.
`addChatParticipantsOptions` describes the request object containing the participants to be added; Use `.setParticipants()` to set the participants to be added to the thread;

- `communicationIdentifier`, required, is the CommunicationIdentifier you've created by using the CommunicationIdentityClient. More info at: [Create A User](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java#create-a-user).
- `displayName`, optional, is the display name for the thread participant.
- `shareHistoryTime`, optional, is the time from which the chat history is shared with the participant. To share history since the inception of the chat thread, set this property to any date equal to, or less than the thread creation time. To share no history previous to when the member was added, set it to the current date. To share partial history, set it to the required date.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L211-L224 -->
```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user1)
    .setDisplayName("Display Name 1");

ChatParticipant secondParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user2)
    .setDisplayName("Display Name 2");

participants.add(firstParticipant);
participants.add(secondParticipant);

chatThreadClient.addParticipants(participants);
```

#### Remove participant

Use `removeParticipant` method to remove a participant from the chat thread identified by chatThreadId.
`identifier` is the CommunicationIdentifier you've created.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L235-L235 -->
```Java
chatThreadClient.removeParticipant(identifier);
```

### Read Receipt Operations

#### Send read receipt

Use `sendReadReceipt` method to post a read receipt event to a chat thread, on behalf of a user.
`chatMessageId` is the unique ID of the chat message that was read.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L244-L245 -->
```Java
String chatMessageId = "Id";
chatThreadClient.sendReadReceipt(chatMessageId);
```

#### Get read receipts

Use `listReadReceipts` to retrieve a paged collection containing the read receipts for a chat thread.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L254-L257 -->
```Java
PagedIterable<ChatMessageReadReceipt> readReceipts = chatThreadClient.listReadReceipts();
readReceipts.forEach(readReceipt -> {
    // You code to handle single readReceipt
});
```

### Typing Notification Operations

#### Send typing notification

Use `sendTypingNotification` method to post a typing notification event to a chat thread, on behalf of a user.

<!-- embedme ./src/samples/java/com/azure/communication/chat/ReadmeSamples.java#L266-L266 -->
```Java
chatThreadClient.sendTypingNotification();
```



## Troubleshooting

In progress.

## Next steps

Check out other client libraries for Azure communication service

<!-- LINKS -->
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[product_docs]: https://docs.microsoft.com/azure/communication-services/
[package]: https://search.maven.org/artifact/com.azure/azure-communication-chat
[api_documentation]: https://aka.ms/java-docs
[source]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/communication/azure-communication-chat/src
