---
title: AMQP 1.0-begärande-/svarsåtgärder i Azure Service Bus
description: Den här artikeln definierar listan över AMQP-begärande/svarsbaserade åtgärder i Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761091"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 i Microsoft Azure Service Bus: begäran-svarsbaserade åtgärder

Den här artikeln definierar listan över Microsoft Azure Service Bus-begärande/svarsbaserade åtgärder. Den här informationen baseras på arbetsutkastet för AMQP Management Version 1.0.  
  
En detaljerad protokollguide för AMQP 1.0 på trådnivå, som förklarar hur Service Bus implementerar och bygger på den tekniska specifikationen FÖR OASIS AMQP, finns [i protokollguiden för AMQP 1.0 i Azure Service Bus and Event Hubs.][AMQP 1.0 protocol guide]  
  
## <a name="concepts"></a>Begrepp  
  
### <a name="entity-description"></a>Entitetsbeskrivning  

En entitetsbeskrivning refererar till antingen en klass för Service Bus [QueueDescription,](/dotnet/api/microsoft.servicebus.messaging.queuedescription) [Klassen TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)eller [Klassen SubscriptionDescription.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)  
  
### <a name="brokered-message"></a>Förmedlat meddelande  

Representerar ett meddelande i Service Bus, som mappas till ett AMQP-meddelande. Mappningen definieras i [protokollguiden för Service Bus AMQP](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Koppla till nod för enhetshantering  

Alla åtgärder som beskrivs i det här dokumentet följer ett mönster för begäran/svar, begränsas till en entitet och kräver att en enhetshanteringsnod kopplas.  
  
### <a name="create-link-for-sending-requests"></a>Skapa länk för att skicka begäranden  

Skapar en länk till hanteringsnoden för att skicka begäranden.  
  
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
Ett transaktionstillstånd kan läggas till som tillval för åtgärder som stöder transaktionen.

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

Tar emot svarsmeddelandet från svarslänken.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Svarsmeddelandet finns i följande form:
  
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
  
### <a name="service-bus-entity-address"></a>Service Bus-enhetsadress  

Service Bus-entiteter måste adresseras enligt följande:  
  
|Entitetstyp|Adress|Exempel|  
|-----------------|-------------|-------------|  
|kö|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|ämne|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|prenumeration|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Meddelandeåtgärder  
  
### <a name="message-renew-lock"></a>Lås för förnyelse av meddelanden  

Utökar låset för ett meddelande med den tid som anges i entitetsbeskrivningen.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
 Meddelandetexten för begäran måste bestå av ett amqp-värdeavsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matris av uuid|Ja|Meddelandelåstokens att förnya.|  

> [!NOTE]
> Låstoken är `DeliveryTag` egenskapen för mottagna meddelanden. Se följande exempel i [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) som hämtar dessa. Token kan också visas i "DeliveryAnnotations" som "x-opt-lock-token" men `DeliveryTag` detta är inte garanterat och bör föredras. 
> 
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK - framgång, annars misslyckades.|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett amqp-värdeavsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Utgångsdatum|matris med tidsstämpel|Ja|Meddelandelåstoken ny förfallodatum som motsvarar begäran låstoken.|  
  
### <a name="peek-message"></a>Granska meddelande  

Tittar på meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Ja|Sekvensnummer som du ska börja kika från.|  
|`message-count`|int|Ja|Maximalt antal meddelanden som ska visas.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1.0 trådkodat meddelande.|  
  
### <a name="schedule-message"></a>Schemalägg meddelande  

Schemalägger meddelanden. Den här åtgärden stöder transaktionen.
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelande-id|sträng|Ja|`amqpMessage.Properties.MessageId`som sträng|  
|session-id|sträng|Inga|`amqpMessage.Properties.GroupId as string`|  
|partition-nyckel|sträng|Inga|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-nyckel|sträng|Inga|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|meddelande|matris med byte|Ja|AMQP 1.0 trådkodat meddelande.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK - framgång, annars misslyckades.|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|sekvens-tal|utbud av långa|Ja|Sekvensnummer för schemalagda meddelanden. Sekvensnummer används för att avbryta.|  
  
### <a name="cancel-scheduled-message"></a>Avbryta schemalagt meddelande  

Avbryter schemalagda meddelanden.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|sekvens-tal|utbud av långa|Ja|Sekvensnummer för schemalagda meddelanden som ska avbrytas.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK - framgång, annars misslyckades.|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|   
  
## <a name="session-operations"></a>Sessionsåtgärder  
  
### <a name="session-renew-lock"></a>Låser för sessionsförnyning  

Utökar låset för ett meddelande med den tid som anges i entitetsbeskrivningen.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|session-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Förfallodagen|timestamp|Ja|Ny utgång.|  
  
### <a name="peek-session-message"></a>Meddelande om en översiktssession  

Tittar på sessionsmeddelanden utan att låsas.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|från sekvens-nummer|long|Ja|Sekvensnummer som du ska börja kika från.|  
|antal meddelanden|int|Ja|Maximalt antal meddelanden som ska visas.|  
|session-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
 Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1.0 trådkodat meddelande.|  
  
### <a name="set-session-state"></a>Ange sessionstillstånd  

Anger tillståndet för en session.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|session-id|sträng|Ja|Sessions-ID.|  
|sessionstillstånd|matris med byte|Ja|Ogenomskinliga binära data.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
### <a name="get-session-state"></a>Hämta sessionstillstånd  

Hämtar tillståndet för en session.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|session-id|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|sessionstillstånd|matris med byte|Ja|Ogenomskinliga binära data.|  
  
### <a name="enumerate-sessions"></a>Räkna upp sessioner  

Räknar upp sessioner på en meddelandeentitet.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|senast uppdaterad tid|timestamp|Ja|Filtrera om du bara vill inkludera sessioner som uppdateras efter en viss tid.|  
|hoppa över|int|Ja|Hoppa över ett antal sessioner.|  
|överst|int|Ja|Maximalt antal sessioner.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – har fler meddelanden<br /><br /> 204: Inget innehåll – inga fler meddelanden|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|hoppa över|int|Ja|Antal överhoppade sessioner om statuskoden är 200.|  
|sessions-ids|matris med strängar|Ja|Matris med sessions-ID:er om statuskoden är 200.|  
  
## <a name="rule-operations"></a>Regelåtgärder  
  
### <a name="add-rule"></a>Lägg till regel  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|regelnamn|sträng|Ja|Regelnamn, exklusive prenumerations- och ämnesnamn.|  
|regelbeskrivning|map|Ja|Regelbeskrivning enligt vad som anges i nästa avsnitt.|  
  
**Regelbeskrivningskartan** måste innehålla följande poster, där **SQL-filter** och **korrelationsfilter** utesluter varandra:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Ja|`sql-filter`, som anges i nästa avsnitt.|  
|korrelationsfilter|map|Ja|`correlation-filter`, som anges i nästa avsnitt.|  
|sql-rule-åtgärd|map|Ja|`sql-rule-action`, som anges i nästa avsnitt.|  
  
Sql-filterkartan måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|uttryck|sträng|Ja|Sql-filteruttryck.|  
  
**Korrelationsfilterkartan** måste innehålla minst en av följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|korrelations-id|sträng|Inga||  
|meddelande-id|sträng|Inga||  
|till|sträng|Inga||  
|svara på|sträng|Inga||  
|etikett|sträng|Inga||  
|session-id|sträng|Inga||  
|svara på-session-id|sträng|Inga||  
|innehållstyp|sträng|Inga||  
|properties|map|Inga|Kartor till Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
**Sql-rule-action-kartan** måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|uttryck|sträng|Ja|Sql-åtgärdsuttryck.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
### <a name="remove-rule"></a>Ta bort regel  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|regelnamn|sträng|Ja|Regelnamn, exklusive prenumerations- och ämnesnamn.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
### <a name="get-rules"></a>Hämta regler

#### <a name="request"></a>Förfrågan

Meddelandet om begäran måste innehålla följande programegenskaper:

|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  

Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|överst|int|Ja|Antalet regler som ska hämtas på sidan.|  
|hoppa över|int|Ja|Antalet regler att hoppa över. Definierar startindexet (+1) i regellistan. | 

#### <a name="response"></a>Svar

Svarsmeddelandet innehåller följande egenskaper:

|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|regler| matris med karta|Ja|Matris med regler. Varje regel representeras av en karta.|

Varje kartpost i matrisen innehåller följande egenskaper:

|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|regelbeskrivning|matris med beskrivna objekt|Ja|`com.microsoft:rule-description:list`med AMQP beskriven kod 0x000013700000004| 

`com.microsoft.rule-description:list`är en matris med beskrivna objekt. Matrisen innehåller följande:

|Index|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
| 0 | matris med beskrivna objekt | Ja | `filter`som anges nedan. |
| 1 | matris med beskrivna objekt | Ja | `ruleAction`som anges nedan. |
| 2 | sträng | Ja | namnet på regeln. |

`filter`kan vara av någon av följande typer:

| Beskrivningsnamn | Beskrivningskod | Värde |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-filter |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelationsfilter |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Sant filter som representerar 1=1 |
| `com.microsoft:false-filter:list` | 0x0000013700000008 | Falskt filter som representerar 1=0 |

`com.microsoft:sql-filter:list`är en beskriven matris som innehåller:

|Index|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
| 0 | sträng | Ja | Sql-filter uttryck |

`com.microsoft:correlation-filter:list`är en beskriven matris som innehåller:

|Index (om det finns)|Värdetyp|Värdeinnehåll|  
|---------|----------------|--------------|
| 0 | sträng | Korrelations-ID |
| 1 | sträng | Meddelande-ID |
| 2 | sträng | Till |
| 3 | sträng | Svara på |
| 4 | sträng | Label (Etikett) |
| 5 | sträng | Sessions-ID |
| 6 | sträng | Svara på sessions-ID|
| 7 | sträng | Innehållstyp |
| 8 | Karta | Karta över programdefinierade egenskaper |

`ruleAction`kan vara någon av följande typer:

| Beskrivningsnamn | Beskrivningskod | Värde |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Tom regelåtgärd - Ingen regelåtgärd finns |
| `com.microsoft:sql-rule-action:list` | 0x00000137000000006 | Sql-regelåtgärd |

`com.microsoft:sql-rule-action:list`är en matris med beskrivna objekt vars första post är en sträng som innehåller SQL-regelåtgärdens uttryck.

## <a name="deferred-message-operations"></a>Uppskjutna meddelandeåtgärder  
  
### <a name="receive-by-sequence-number"></a>Ta emot efter sekvensnummer  

Tar emot uppskjutna meddelanden efter sekvensnummer.  
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|sekvens-tal|utbud av långa|Ja|Sekvensnummer.|  
|mottagare-settle-läge|ubyte (på)|Ja|**Mottagarsetningsläge** enligt vad som anges i AMQP-kärnan v1.0.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|  
  
Brödtexten för svarsmeddelandet måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|lås-token|uuid|Ja|Lås token `receiver-settle-mode` om är 1.|  
|meddelande|matris med byte|Ja|AMQP 1.0 trådkodat meddelande.|  
  
### <a name="update-disposition-status"></a>Uppdatera dispositionsstatus  

Uppdaterar dispositionsstatus för uppskjutna meddelanden. Den här åtgärden stöder transaktioner.
  
#### <a name="request"></a>Förfrågan  

Meddelandet om begäran måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|Drift|sträng|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Inga|Timeout för driftserver i millisekunder.|  
  
Meddelandetexten för begäran måste bestå av ett **amqp-värdeavsnitt** som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|disposition-status|sträng|Ja|Avslutade<br /><br /> Övergivna<br /><br /> tillfälligt|  
|lås-tokens|matris av uuid|Ja|Meddelandelåstokens för att uppdatera dispositionsstatus.|  
|dödbrevs-anledning|sträng|Inga|Kan anges om dispositionsstatus är inställd på **att pausas**.|  
|dödbrev-beskrivning|sträng|Inga|Kan anges om dispositionsstatus är inställd på **att pausas**.|  
|egenskaper-till-modifiera|map|Inga|Lista över meddelandeegenskaper för Service Bus som ska ändras.|  
  
#### <a name="response"></a>Svar  

Svarsmeddelandet måste innehålla följande programegenskaper:  
  
|Nyckel|Värdetyp|Krävs|Värdeinnehåll|  
|---------|----------------|--------------|--------------------|  
|statusKoda|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – framgång, annars misslyckades|  
|statusBeskrivning|sträng|Inga|Beskrivning av status.|

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP och Service Bus finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]
* [AMQP i servicebuss för Windows Server]

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md
[AMQP i servicebuss för Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
