---
title: AMQP 1.0 i Azure Service Bus-begäran-svar-baserade åtgärder | Microsoft Docs
description: Lista över Microsoft Azure Service Bus-begäran/svar-baserade åtgärder.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c22ba0b57ed1161e1f7e2082d2ba21f27b656da1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121578"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 i Microsoft Azure Service Bus: begäran-svar-baserade åtgärder

Den här artikeln definierar en lista med Microsoft Azure Service Bus-begäran/svar-baserade åtgärder. Den här informationen baseras på AMQP Management Version 1.0 fungerande utkastet.  
  
En detaljerad överföring nivå AMQP 1.0-protokollet guide, som beskriver hur Service Bus implementerar och bygger på OASIS AMQP tekniska specifikationer, finns det [AMQP 1.0 i Azure Service Bus och Händelsehubbar protokollet guiden][amqp 1.0-protokollguide].  
  
## <a name="concepts"></a>Begrepp  
  
### <a name="entity-description"></a>Entitetsbeskrivning  

En entitetsbeskrivning av refererar till antingen ett Service Bus [QueueDescription klass](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription klass](/dotnet/api/microsoft.servicebus.messaging.topicdescription), eller [SubscriptionDescription klass](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) objekt.  
  
### <a name="brokered-message"></a>Asynkrona meddelanden  

Representerar ett meddelande i Service Bus, som är mappad till en AMQP-meddelande. Mappningen är definierad i den [Service Bus AMQP-protokollguide](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Ansluta till entitet hanteringsnod  

Alla åtgärder som beskrivs i detta dokument följer ett mönster för begäran/svar, är begränsade till en entitet och kräver den kopplas till en nod för entiteten.  
  
### <a name="create-link-for-sending-requests"></a>Skapa länk för att skicka begäran  

Skapar en länk till hanteringsnoden för att skicka begäran.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Skapa länk för att ta emot svar  

Skapar en länk för att ta emot svar från hanteringsnoden.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Överföra ett meddelande om begäran  

Överför ett meddelande om begäran.  
En Transaktionstillståndet kan läggas till om du vill för åtgärder som har stöd för transaktionen.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Ta emot ett svarsmeddelande  

Tar emot svarsmeddelandet från länken svar.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Svarsmeddelandet är i följande format:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus-adress för entitet  

Service Bus-entiteter måste åtgärdas på följande sätt:  
  
|Entitetstyp|Adress|Exempel|  
|-----------------|-------------|-------------|  
|kö|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|ämne|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|prenumeration|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Meddelandeåtgärder  
  
### <a name="message-renew-lock"></a>Förnya Lås för meddelandet  

Utökar låset på meddelandet genom att den tid som anges i Entitetsbeskrivningen.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
 Meddelandetexten begäran måste bestå av ett avsnitt för amqp-värde som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matris med uuid|Ja|Lås token för meddelandet att förnya.|  

> [!NOTE]
> Lås token är den `DeliveryTag` egenskapen på mottagna meddelanden. Se följande exempel i den [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) som hämtar dessa. Token kan också förekomma i DeliveryAnnotations som 'x-opt-Lås-token' men, detta är inte säkert och `DeliveryTag` som ska användas. 
> 
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av ett avsnitt för amqp-värde som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|förfallodatum|matris med tidsstämpel|Ja|Lås token nya förfallodatum för meddelanden motsvarar Lås token för begäran.|  
  
### <a name="peek-message"></a>Granska meddelande  

Tittar meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|lång|Ja|Serienummer som du vill starta peek från.|  
|`message-count`|int|Ja|Maximalt antal meddelanden för att granska.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över maps|Ja|Lista med meddelanden där varje kartan är ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1.0 wire-kodat meddelande.|  
  
### <a name="schedule-message"></a>Schema-meddelande  

Schemalägger meddelanden. Den här åtgärden har stöd för transaktionen.
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över maps|Ja|Lista med meddelanden där varje kartan är ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|message-id|sträng|Ja|`amqpMessage.Properties.MessageId` som sträng|  
|sessions-id|sträng|Nej|`amqpMessage.Properties.GroupId as string`|  
|partitionsnyckel|sträng|Nej|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|sträng|Nej|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|meddelande|matris med byte|Ja|AMQP 1.0 wire-kodat meddelande.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med långa|Ja|Sekvensnumret för schemalagda meddelanden. Sekvensnummer används för att avbryta.|  
  
### <a name="cancel-scheduled-message"></a>Avbryt schemalagda meddelande  

Avbryter schemalagda meddelanden.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med långa|Ja|Sekvensnummer schemalagda meddelanden att avbryta.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med långa|Ja|Sekvensnumret för schemalagda meddelanden. Sekvensnummer används för att avbryta.|  
  
## <a name="session-operations"></a>Sessionen åtgärder  
  
### <a name="session-renew-lock"></a>Förnya låset för sessionen  

Utökar låset på meddelandet genom att den tid som anges i Entitetsbeskrivningen.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessions-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|upphörande|tidsstämpel|Ja|Ny upphör att gälla.|  
  
### <a name="peek-session-message"></a>Granska Session meddelande  

Tittar sessions-meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|från sekvensnummer|lång|Ja|Serienummer som du vill starta peek från.|  
|Antal meddelanden|int|Ja|Maximalt antal meddelanden för att granska.|  
|sessions-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över maps|Ja|Lista med meddelanden där varje kartan är ett meddelande.|  
  
 Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1.0 wire-kodat meddelande.|  
  
### <a name="set-session-state"></a>Set-sessionstillstånd  

Anger tillståndet för en session.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessions-id|sträng|Ja|Sessions-ID.|  
|sessionstillstånd|byte-matris|Ja|Täckande binära data.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
### <a name="get-session-state"></a>Get-sessionstillstånd  

Hämtar tillståndet för en session.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessions-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessionstillstånd|byte-matris|Ja|Täckande binära data.|  
  
### <a name="enumerate-sessions"></a>Räkna upp sessioner  

Räknar upp sessioner på en meddelandeentitet.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|tidsstämpel|Ja|Filtrera om du vill inkludera endast sessioner som har uppdaterats efter en viss tid.|  
|hoppa över|int|Ja|Hoppa över ett antal sessioner.|  
|längst upp|int|Ja|Maximalt antal sessioner.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|hoppa över|int|Ja|Antal överhoppade sessioner om statuskoden är 200.|  
|sessioner-ID: n|matris med strängar|Ja|Matris med sessions-ID. Om statuskoden är 200.|  
  
## <a name="rule-operations"></a>Åtgärder för regeln  
  
### <a name="add-rule"></a>Lägg till regel  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|namn|sträng|Ja|Regelnamn inkluderar inte prenumerationen och avsnittet namn.|  
|rule-description|map|Ja|Beskrivning av regel som anges i nästa avsnitt.|  
  
Den **beskrivning av regel** karta måste innehålla följande poster där **sql-filter** och **korrelationsfiltret** kan inte anges samtidigt:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Ja|`sql-filter`, som angetts i nästa avsnitt.|  
|correlation-filter|map|Ja|`correlation-filter`, som angetts i nästa avsnitt.|  
|sql-rule-action|map|Ja|`sql-rule-action`, som angetts i nästa avsnitt.|  
  
Sql-filter kartan måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|uttryck|sträng|Ja|SQL-filteruttryck.|  
  
Den **korrelationsfiltret** karta måste innehålla minst en av följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|Korrelations-id|sträng|Nej||  
|message-id|sträng|Nej||  
|till|sträng|Nej||  
|Svara till|sträng|Nej||  
|etikett|sträng|Nej||  
|sessions-id|sträng|Nej||  
|svars-till-sessions-id|sträng|Nej||  
|innehållstyp|sträng|Nej||  
|properties|map|Nej|Maps till Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Den **sql Regelåtgärd** karta måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|uttryck|sträng|Ja|Uttryck för SQL-åtgärd.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
### <a name="remove-rule"></a>Ta bort regel  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|namn|sträng|Ja|Regelnamn inkluderar inte prenumerationen och avsnittet namn.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
### <a name="get-rules"></a>Hämta regler

#### <a name="request"></a>Förfrågan

Meddelandet med begäran måste innehålla följande egenskaper för program:

|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  

Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|längst upp|int|Ja|Antal regler för att hämta på sidan.|  
|hoppa över|int|Ja|Antal regler för att hoppa över. Definierar startIndex (+ 1) i listan med regler. | 

#### <a name="response"></a>Svar

Svarsmeddelandet innehåller följande egenskaper:

|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|regler| matris med karta|Ja|Matris med regler. Varje regel representeras av en karta.|

Varje post i matrisen innehåller följande egenskaper:

|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|rule-description|matris med beskrivs objekt|Ja|`com.microsoft:rule-description:list` Beskrivningen kod 0x0000013700000004 med AMQP| 

`com.microsoft.rule-description:list` är en matris med objekt som beskrivs. Matrisen innehåller följande:

|Index|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
| 0 | matris med beskrivs objekt | Ja | `filter` som angetts nedan. |
| 1 | matris med beskrivs objekt | Ja | `ruleAction` som angetts nedan. |
| 2 | sträng | Ja | namnet på regeln. |

`filter` kan vara i något av följande typer:

| Descriptor namn | Descriptor kod | Värde |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-filter |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelationsfiltret |
| `com.microsoft:true-filter:list` | 0x000001370000007 | SANT filter som representerar 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | FALSKT filter som representerar 1 = 0 |

`com.microsoft:sql-filter:list` är en beskrivs matris som innehåller:

|Index|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
| 0 | sträng | Ja | SQL-filteruttryck |

`com.microsoft:correlation-filter:list` är en beskrivs matris som innehåller:

|Index (om det finns)|Värdetyp|Värdet innehållet|  
|---------|----------------|--------------|
| 0 | sträng | Korrelations-ID |
| 1 | sträng | Meddelande-ID |
| 2 | sträng | Till |
| 3 | sträng | Svara |
| 4 | sträng | Label (Etikett) |
| 5 | sträng | Sessions-ID |
| 6 | sträng | Svar på sessions-ID|
| 7 | sträng | Innehållstyp |
| 8 | Karta | Karta över egenskaper för program som har definierats |

`ruleAction` kan vara något av följande typer:

| Descriptor namn | Descriptor kod | Värde |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Tom Regelåtgärd - ingen Regelåtgärd som är tillgänglig |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL-Regelåtgärd |

`com.microsoft:sql-rule-action:list` är en matris med beskrivs objekt vars första post är en sträng som innehåller SQL-Regelåtgärd uttryck.

## <a name="deferred-message-operations"></a>Uppskjutna meddelandeåtgärder  
  
### <a name="receive-by-sequence-number"></a>Ta emot genom sekvensnummer  

Tar emot uppskjutna meddelanden av sekvensnummer.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med långa|Ja|Sekvensnummer.|  
|receiver-settle-mode|ubyte|Ja|**Mottagare löser in** läge som anges i AMQP core v1.0.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|Nej|Beskrivning av status.|  
  
Svarets meddelandetext måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över maps|Ja|Lista med meddelanden där varje kartan representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|Lås token|uuid|Ja|Lås token om `receiver-settle-mode` är 1.|  
|meddelande|matris med byte|Ja|AMQP 1.0 wire-kodat meddelande.|  
  
### <a name="update-disposition-status"></a>Uppdateringsstatus för disposition  

Uppdaterar disposition status för uppskjutna meddelanden. Den här åtgärden stöder transaktioner.
  
#### <a name="request"></a>Förfrågan  

Meddelandet med begäran måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärd|sträng|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|disposition-status|sträng|Ja|completed<br /><br /> övergivna<br /><br /> Har pausats|  
|Lås token|matris med uuid|Ja|Meddelandet Lås token för att uppdatera disposition status.|  
|deadletter-reason|sträng|Nej|Kan anges om disposition status anges till **pausats**.|  
|deadletter-description|sträng|Nej|Kan anges om disposition status anges till **pausats**.|  
|Egenskaper för att ändra|map|Nej|Lista över Service Bus brokered meddelandeegenskaper som ska ändras.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande egenskaper för program:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|Nej|Beskrivning av status.|

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP och Service Bus finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]
* [AMQP i Service Bus för Windows Server]

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md
[AMQP i Service Bus för Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
