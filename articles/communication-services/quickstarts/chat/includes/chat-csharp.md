---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816622"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:
- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installera [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste registrera resurs **slut punkten** för den här snabb starten.
- En [åtkomsttoken för användare](../../access-tokens.md). Var noga med att ange omfånget till "chatt" och anteckna token-strängen och userId-strängen.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `ChatQuickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: **program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installera paketet

Installera klient biblioteket för Azure Communication Chat för .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för C#.

| Namn                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton. |

## <a name="create-a-chat-client"></a>Skapa en Chat-klient

Om du vill skapa en chatt-klient använder du kommunikations tjänstens slut punkt och den åtkomsttoken som genererades som en del av de nödvändiga stegen. Du måste använda- `CommunicationIdentityClient` klassen från `Administration` klient biblioteket för att skapa en användare och utfärda en token som skickas till din Chat-klient. Läs mer om [åtkomsttoken för användare](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `createChatThread` metoden för att skapa en chatt-tråd.
- Används `topic` för att ge ett ämne till den här chatten. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `UpdateThread` funktionen.
- Använd `members` egenskap för att skicka en lista med `ChatThreadMember` objekt som ska läggas till i chatt-tråden. `ChatThreadMember`Objektet initieras med ett `CommunicationUser` objekt. Om du vill hämta ett `CommunicationUser` objekt måste du skicka ett åtkomst-ID som du skapade genom att följa anvisningarna för att [skapa en användare](../../access-tokens.md#create-an-identity)

Svaret `chatThreadClient` används för att utföra åtgärder på den skapade chatt-tråden: lägga till medlemmar i chatten, skicka ett meddelande, ta bort ett meddelande, osv. Den innehåller det `Id` attribut som är det unika ID: t för chatt-tråden. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd
`GetChatThreadClient`Metoden returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till medlemmar, skicka meddelande, osv. threadId är det unika ID: t för den befintliga chatt tråden.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Använd `SendMessage` metoden för att skicka ett meddelande till en tråd som identifieras av threadId.

- Används `content` för att tillhandahålla chatt-meddelandets innehåll, det krävs.
- Används `priority` för att ange meddelandets prioritets nivå, till exempel normal eller hög, om den inte anges används "normal".
- Används `senderDisplayName` för att ange visnings namnet på avsändaren, om inget anges, används inget tomt namn.

`SendChatMessageResult` är svaret som returnerades från att skicka ett meddelande, det innehåller ett ID, som är det unika ID: t för meddelandet.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta Chat-meddelanden genom att avsöka `GetMessages` metoden på chatt-trådens klient vid angivna intervall.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` tar en valfri `DateTimeOffset` parameter. Om förskjutningen anges visas meddelanden som har tagits emot, uppdaterats eller tagits bort efter det. Observera att meddelanden som mottagits före förskjutnings tiden, men som redige ras eller tas bort efter att de också kommer att returneras.

`GetMessages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av `UpdateMessage` och `DeleteMessage` . För borttagna meddelanden `chatMessage.DeletedOn` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `chatMessage.EditedOn` returnerar en datetime som anger när meddelandet redigerades. Det går att komma åt den ursprungliga tiden för att skapa meddelanden med `chatMessage.CreatedOn` , och det kan användas för att ordna meddelandena.

`GetMessages` returnerar olika typer av meddelanden som kan identifieras av `chatMessage.Type` . Dessa typer är:

- `Text`: Vanligt chatt-meddelande som skickas av en tråd medlem.

- `ThreadActivity/TopicUpdate`: System meddelande som anger att ämnet har uppdaterats.

- `ThreadActivity/AddMember`: System meddelande som anger att en eller flera medlemmar har lagts till i chatt-tråden.

- `ThreadActivity/DeleteMember`: System meddelande som anger att en medlem har tagits bort från chatt-tråden.

Mer information finns i [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Uppdatera ett meddelande

Du kan uppdatera ett meddelande som redan har skickats genom att anropa `UpdateMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Ta bort ett meddelande

Du kan ta bort ett meddelande genom att `DeleteMessage` anropa `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Lägg till en användare som medlem i Chat-tråden

När en tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att kunna skicka meddelanden till tråden och lägga till/ta bort andra medlemmar. Innan du anropar `AddMembers` bör du se till att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

Använd `AddMembers` metoden för att lägga till tråd medlemmar i den tråd som identifieras av threadId.

 - Används `members` för att visa en lista över medlemmar som ska läggas till i chatten.
 - `User`, krävs, är den identitet som du får för den nya användaren.
 - `DisplayName`, valfritt är visnings namnet för tråd medlemmen.
 - `ShareHistoryTime`, valfri tid från vilken chatt-historiken delas med medlemmen. Om du vill dela historiken efter början av chatt-tråden ställer du in den på DateTime. MinValue. Om du inte vill dela någon historik tidigare än när medlemmen lades till, ställer du in den på den aktuella tiden. Om du vill dela partiell historik ställer du in den på en tidpunkt mellan tråd skapande och aktuell tid.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

På samma sätt som du lägger till en användare i en tråd kan du ta bort användare från en chatt-tråd. För att göra det måste du spåra identiteten (CommunicationUser) för de medlemmar som du har lagt till.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `dotnet run` kommandot.

```console
dotnet run
```
