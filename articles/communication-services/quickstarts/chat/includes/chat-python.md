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
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523858"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installera [python](https://www.python.org/downloads/)
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste registrera resurs **slut punkten** för den här snabb starten
- En [åtkomsttoken för användare](../../access-tokens.md). Var noga med att ange omfånget till "chatt" och anteckna token-strängen och userId-strängen.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Använd en text redigerare för att skapa en fil med namnet **Start-chat.py** i projektets rot Katalog och lägga till strukturen för programmet, inklusive grundläggande undantags hantering. Du kommer att lägga till alla käll koder för den här snabb starten till den här filen i följande avsnitt.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Installera klient bibliotek

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för python.

| Namn                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton. |

## <a name="create-a-chat-client"></a>Skapa en Chat-klient

Om du vill skapa en chatt-klient använder du kommunikations tjänstens slut punkt och den `Access Token` som har genererats som en del av de nödvändiga stegen. Läs mer om [åtkomsttoken för användare](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `create_chat_thread` metoden för att skapa en chatt-tråd.

- Använd `topic` för att ge ett tråd ämne. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `update_thread` funktionen.
- Använd `members` för att visa en lista över `ChatThreadMember` som ska läggas till i chatten. `ChatThreadMember` tar med `CommunicationUser` typ som det `user` du fick när du skapade genom att [skapa en användare](../../access-tokens.md#create-an-identity)

Svaret `chat_thread_client` används för att utföra åtgärder på den nyskapade chatt-tråden som att lägga till medlemmar i chatt-tråden, skicka meddelande, ta bort meddelande, osv. Den innehåller en `thread_id` egenskap som är det unika ID: t för chatt-tråden.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd
Metoden get_chat_thread_client returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till medlemmar, skicka meddelande osv. thread_id är det unika ID: t för den befintliga chatt tråden.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Använd `send_message` metoden för att skicka ett meddelande till en chatt-tråd som du nyss skapade, som identifieras av threadId.

- Används `content` för att tillhandahålla chatt-meddelandets innehåll.
- Används `priority` för att ange meddelandets prioritets nivå, till exempel "normal" eller "hög"; den här egenskapen kan användas för att Visa användar gränssnitts indikatorn för mottagaren i din app för att uppmärksamma meddelandet eller köra anpassad affärs logik.
- Används `senderDisplayName` för att ange visnings namnet på avsändaren.

Svaret `SendChatMessageResult` innehåller ett "ID", vilket är det unika ID: t för meddelandet.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta chatt meddelanden genom att avsöka `list_messages` metoden vid angivna intervall.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av `update_message` och `delete_message` . För borttagna meddelanden `ChatMessage.deleted_on` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `ChatMessage.edited_on` returnerar en datetime som anger när meddelandet redigerades. Det går att få åtkomst till den ursprungliga tiden för att skapa meddelanden med `ChatMessage.created_on` som kan användas för att ordna meddelandena.

`list_messages` returnerar olika typer av meddelanden som kan identifieras av `ChatMessage.type` . Dessa typer är:

- `Text`: Vanligt chatt-meddelande som skickas av en tråd medlem.

- `ThreadActivity/TopicUpdate`: System meddelande som anger att ämnet har uppdaterats.

- `ThreadActivity/AddMember`: System meddelande som anger att en eller flera medlemmar har lagts till i chatt-tråden.

- `ThreadActivity/DeleteMember`: System meddelande som anger att en medlem har tagits bort från chatt-tråden.

Mer information finns i [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Lägg till en användare som medlem i Chat-tråden

När en chatt-tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att kunna skicka meddelanden till chatt-tråden och lägga till/ta bort andra medlemmar. Innan du anropar `add_members` -metoden kontrollerar du att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

Använd `add_members` metoden för att lägga till tråd medlemmar i den tråd som identifieras av threadId.

- Används `members` för att visa en lista över medlemmar som ska läggas till i chatten.
- `user`, krävs, är `CommunicationUser` du skapade av `CommunicationIdentityClient` vid [skapa en användare](../../access-tokens.md#create-an-identity)
- `display_name`, valfritt är visnings namnet för tråd medlemmen.
- `share_history_time`, valfritt, är den tid som chatt-historiken delas med medlemmen. Om du vill dela historiken på grund av att chatten är i gång, anger du den här egenskapen till ett datum som är lika med eller mindre än tiden för tråd skapande. Om du inte vill dela någon historik tidigare när medlemmen lades till, ställer du in den på det aktuella datumet. Om du vill dela partiell historik anger du ett mellanliggande datum.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

På samma sätt som du lägger till en medlem kan du också ta bort medlemmar från en tråd. För att kunna ta bort måste du spåra ID: n för de medlemmar som du har lagt till.

Använd `remove_member` metoden för att ta bort tråd medlem från tråden som identifieras av threadId.
- `user` är CommunicationUser som ska tas bort från tråden.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `python` kommandot.

```console
python start-chat.py
```
