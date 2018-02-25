---
title: "AMQP 1.0 i Azure Service Bus-begäran-svar-baserade åtgärder | Microsoft Docs"
description: "Lista över Microsoft Azure Service Bus begäran/svar-baserade åtgärder."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: d72a4de8591898a55e4225ace154fd5ed53e6f91
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 i Microsoft Azure Service Bus: begäran-svar-baserade åtgärder

Den här artikeln definierar lista över Microsoft Azure Service Bus begäran/svar-baserade åtgärder. Den här informationen baseras på det AMQP Management Version 1.0 utkastet.  
  
En detaljerad överföring nivå AMQP 1.0-protokollet guide, som beskriver hur Service Bus implementerar och bygger på OASIS AMQP tekniska specifikationer, finns det [AMQP 1.0 i Azure Service Bus och Händelsehubbar protokollet guiden] [ AMQP 1.0-protokollet guiden].  
  
## <a name="concepts"></a>Begrepp  
  
### <a name="entity-description"></a>Entiteten beskrivning  

En beskrivning av entiteten refererar till antingen en Service Bus [QueueDescription klassen](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription klassen](/dotnet/api/microsoft.servicebus.messaging.topicdescription), eller [SubscriptionDescription klassen](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) objekt.  
  
### <a name="brokered-message"></a>Asynkrona meddelanden  

Representerar ett meddelande i Service Bus, som är mappad till en AMQP-meddelande. Mappningen har definierats i den [Service Bus AMQP protokollet guiden](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Anslut till entiteten hanteringsnod  

Alla åtgärder som beskrivs i det här dokumentet följer ett mönster i begäran och svar är begränsade till en entitet och kräver att den kopplas till en entitet hanteringsnod.  
  
### <a name="create-link-for-sending-requests"></a>Skapa länk för att skicka begäranden  

Skapar en länk till noden hantering för att skicka begäranden.  
  
```  
requestLink = session.attach(     
role: SENDER,   
    target: { address: "<entity address>/$management" },   
    source: { address: ""<my request link unique address>" }   
)  
  
```  
  
### <a name="create-link-for-receiving-responses"></a>Skapa länk för att ta emot svar  

Skapar en länk för att ta emot svar från noden hantering.  
  
```  
responseLink = session.attach(    
role: RECEIVER,   
    source: { address: "<entity address>/$management" }   
    target: { address: "<my response link unique address>" }   
)  
  
```  
  
### <a name="transfer-a-request-message"></a>Överföra ett meddelande om begäran  

Överför ett meddelande om begäran.  
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
        )  
```  
  
### <a name="receive-a-response-message"></a>Ta emot ett svarsmeddelande  

Tar emot svarsmeddelandet från länken svar.  
  
```  
responseMessage = responseLink.receiveTransfer()  
```  
  
Svarsmeddelandet har följande format:
  
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
  
### <a name="service-bus-entity-address"></a>Adressen för Service Bus-entitet  

Service Bus-entiteter måste åtgärdas på följande sätt:  
  
|Entitetstyp|Adress|Exempel|  
|-----------------|-------------|-------------|  
|Kön|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|Avsnittet|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|prenumeration|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Meddelandeåtgärder  
  
### <a name="message-renew-lock"></a>Message Renew Lock  

Utökar låset för meddelanden av den tid som anges i Entitetsbeskrivningen.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
 Meddelandetexten begäran måste bestå av en amqp-värde-avsnitt som innehåller en karta med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matris med uuid|Ja|Meddelandet lock-tokens för att förnya.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en amqp-värde-avsnitt som innehåller en karta med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|förfallodatum|matris med tidsstämpel|Ja|Meddelandet Lås nya giltighetstid för token motsvarar begära lock-token.|  
  
### <a name="peek-message"></a>Granska meddelande  

Tittar meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|lång|Ja|Sekvensnummer som du vill starta titt.|  
|`message-count`|int|Ja|Maximalt antal meddelanden för att granska.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 0xcc: ingen innehåll – inga fler meddelanden|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över mappar|Ja|Listan över meddelanden där varje kartan representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1.0 överföring-kodat meddelande.|  
  
### <a name="schedule-message"></a>Schema-meddelande  

Schemalägger meddelanden.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över mappar|Ja|Listan över meddelanden där varje kartan representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|message-id|sträng|Ja|`amqpMessage.Properties.MessageId` som en sträng|  
|sessions-id|sträng|Ja|`amqpMessage.Properties.GroupId as string`|  
|partition-key|sträng|Ja|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|meddelande|matris med byte|Ja|AMQP 1.0 överföring-kodat meddelande.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp värdet** avsnitt som innehåller en karta med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med lång|Ja|Sekvensnummer för schemalagda meddelanden. Numret används för att avbryta.|  
  
### <a name="cancel-scheduled-message"></a>Avbryt schemalagda meddelande  

Avbryter schemalagda meddelanden.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med lång|Ja|Sekvensnummer schemalagda meddelanden att avbryta.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp värdet** avsnitt som innehåller en karta med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med lång|Ja|Sekvensnummer för schemalagda meddelanden. Numret används för att avbryta.|  
  
## <a name="session-operations"></a>Sessionen åtgärder  
  
### <a name="session-renew-lock"></a>Sessionen förnya Lås  

Utökar låset för meddelanden av den tid som anges i Entitetsbeskrivningen.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessions-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 0xcc: ingen innehåll – inga fler meddelanden|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp värdet** avsnitt som innehåller en karta med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|upphörande|tidsstämpel|Ja|Nya upphör att gälla.|  
  
### <a name="peek-session-message"></a>Granska Session meddelande  

Tittar sessions-meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|lång|Ja|Sekvensnummer som du vill starta titt.|  
|antalet meddelanden|int|Ja|Maximalt antal meddelanden för att granska.|  
|sessions-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 0xcc: ingen innehåll – inga fler meddelanden|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp värdet** avsnitt som innehåller en karta med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över mappar|Ja|Listan över meddelanden där varje kartan representerar ett meddelande.|  
  
 Kartan som representerar ett meddelande måste innehålla följande:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1.0 överföring-kodat meddelande.|  
  
### <a name="set-session-state"></a>Ange sessionstillstånd  

Anger tillståndet för en session.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessions-id|sträng|Ja|Sessions-ID.|  
|sessionstillstånd|byte-matris|Ja|Täckande binära data.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
### <a name="get-session-state"></a>Get-sessionstillstånd  

Hämtar status för en session.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessions-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sessionstillstånd|byte-matris|Ja|Täckande binära data.|  
  
### <a name="enumerate-sessions"></a>Räkna upp sessioner  

Räknar sessioner på en meddelandeentitet.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|tidsstämpel|Ja|Filtreras så att endast de sessioner som har uppdaterats efter en viss tidpunkt.|  
|skip|int|Ja|Hoppa över ett antal sessioner.|  
|Upp|int|Ja|Maximalt antal sessioner.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 0xcc: ingen innehåll – inga fler meddelanden|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|skip|int|Ja|Antal överhoppade sessioner om statuskoden 200.|  
|sessioner-ID|Strängmatris|Ja|Matris med sessions-ID. Om statuskoden 200.|  
  
## <a name="rule-operations"></a>Åtgärder för regeln  
  
### <a name="add-rule"></a>Lägg till regel  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|Regelnamnet|sträng|Ja|Regelnamnet inte inklusive namn för ämnet och prenumerationen.|  
|Beskrivning av regel|karta|Ja|Beskrivning av regel som anges i nästa avsnitt.|  
  
Den **beskrivning av regel** karta måste innehålla följande poster där **sql-filter** och **Korrelations-filter** kan inte anges samtidigt:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sql-filter|karta|Ja|`sql-filter`, som anges i nästa avsnitt.|  
|correlation-filter|karta|Ja|`correlation-filter`, som anges i nästa avsnitt.|  
|sql-rule-action|karta|Ja|`sql-rule-action`, som anges i nästa avsnitt.|  
  
Sql-filter karta måste innehålla följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|expression|sträng|Ja|Filteruttrycket för SQL.|  
  
Den **Korrelations-filter** karta måste innehålla minst en av följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|Korrelations-id|sträng|Nej||  
|message-id|sträng|Nej||  
|till|sträng|Nej||  
|Svara till|sträng|Nej||  
|Etikett|sträng|Nej||  
|sessions-id|sträng|Nej||  
|Svara till session-id|sträng|Nej||  
|innehållstyp|sträng|Nej||  
|properties|karta|Nej|Mappas till Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties).|  
  
Den **sql Regelåtgärd** karta måste innehålla följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|expression|sträng|Ja|SQL-uttryck för åtgärden.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
### <a name="remove-rule"></a>Ta bort regel  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|Regelnamnet|sträng|Ja|Regelnamnet inte inklusive namn för ämnet och prenumerationen.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
## <a name="deferred-message-operations"></a>Uppskjuten meddelandeåtgärder  
  
### <a name="receive-by-sequence-number"></a>Ta emot med sekvensnumret  

Tar emot uppskjutna meddelanden efter sekvensnummer.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|sekvensnummer|matris med lång|Ja|Sekvensnummer.|  
|receiver-settle-mode|ubyte|Ja|**Mottagaren kvitta** läge som anges i AMQP core v1.0.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|StatusDescription|sträng|Nej|Beskrivning av status.|  
  
Meddelandetexten svar måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över mappar|Ja|Listan över meddelanden där varje kartan representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|lock-token|UUID|Ja|Lås token om `receiver-settle-mode` är 1.|  
|meddelande|matris med byte|Ja|AMQP 1.0 överföring-kodat meddelande.|  
  
### <a name="update-disposition-status"></a>Uppdatera status för disposition  

Uppdaterar disposition statusen för uppskjuten meddelanden.  
  
#### <a name="request"></a>Förfrågan  

Begärandemeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|åtgärden|sträng|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nej|Timeout för åtgärden server i millisekunder.|  
  
Meddelandetexten begäran måste bestå av en **amqp-värdet** avsnitt som innehåller en **kartan** med följande uppgifter:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|disposition-status|sträng|Ja|Slutförd<br /><br /> Avbrutna<br /><br /> avbruten|  
|Lås-token|matris med uuid|Ja|Meddelandet lock-tokens för att uppdatera disposition status.|  
|deadletter-reason|sträng|Nej|Du kan ange om disposition status anges till **avbruten**.|  
|systemkön-beskrivning|sträng|Nej|Du kan ange om disposition status anges till **avbruten**.|  
|properties-to-modify|karta|Nej|Lista över Service Bus asynkrona egenskaper för att ändra.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla de följande egenskaperna:  
  
|Nyckel|Värdetyp|Krävs|Värdet innehållet|  
|---------|----------------|--------------|--------------------|  
|statuskod|int|Ja|HTTP-svarskoden [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|StatusDescription|sträng|Nej|Beskrivning av status.|

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP och Service Bus finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]
* [AMQP i Service Bus för Windows Server]

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md
[AMQP i Service Bus för Windows Server]: https://msdn.microsoft.com/library/dn574799.asp