---
title: Azure Storage-köer och Service Bus-köer jämförelser och skillnader | Microsoft Docs
description: Analyserar skillnader och likheter mellan två olika sorters köerna som erbjuds av Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c59d79a7c6ac0590861c99daa01438b184cd71ff
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852804"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage-köer och Service Bus-köer – jämförelser och skillnader
Den här artikeln analyserar både skillnader och likheter mellan de två typerna av köer som erbjuds av Microsoft Azure idag: Storage-köer och Service Bus-köer. Med hjälp av informationen kan du jämföra de olika teknikerna och fatta klokare beslut när du ska avgöra vilken lösning som passar dig bäst.

## <a name="introduction"></a>Introduktion
Azure stöder två typer av kön mekanismer: **Lagringsköer** och **Service Bus-köer**.

**Lagringsköer**, vilket är en del av den [Azure storage](https://azure.microsoft.com/services/storage/) infrastruktur, funktionen ett enkelt REST-baserad GET/PUT/PEEK-gränssnitt för att tillhandahålla pålitlig, beständig meddelandetrafik inom och mellan tjänster.

**Service Bus-köer** är en del av en bredare [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastruktur som stöder queuing samt publicera/prenumerera på och mer avancerade integration mönster. Läs mer om Service Bus-köer/ämnen/prenumerationer, den [översikt över Service Bus](service-bus-messaging-overview.md).

Även om båda queuing teknikerna finns samtidigt, introducerades lagringsköer först som en dedikerad kö storage mekanism som bygger på Azure Storage-tjänster. Service Bus-köer är byggda på bredare meddelandeinfrastrukturen för att integrera program eller programkomponenter som kan omfatta flera kommunikationsprotokoll, datakontrakt, betrodda domäner och nätverksmiljöer.

## <a name="technology-selection-considerations"></a>Överväganden för val av teknik
Både Storage-köer och Service Bus-köer är implementeringar av Meddelandeköer tjänst som för närvarande erbjuds av Microsoft Azure. Var och en har ett något annorlunda funktionsuppsättning, vilket innebär att du kan välja en av eller använda båda beroende på behoven i din lösning eller business-tekniska problem som du vill lösa.

När du fastställer vilken queuing teknik passar syftet för den aktuella lösningen, bör de här rekommendationerna i lösningsarkitekter och utvecklare. Mer information finns i nästa avsnitt.

Som utvecklare/solution architect, **bör du använda lagringsköer** när:

* Programmet måste lagra över 80 GB av meddelanden i en kö.
* Programmet vill spåra förloppet för bearbetning av ett meddelande i kön. Detta är användbart om den arbetsroll som bearbetar ett meddelande som kraschar. En efterföljande worker kan sedan använda informationen för att fortsätta från där den tidigare worker slutade.
* Du behöver sida i loggarna för alla transaktioner som körs mot din köer.

Som utvecklare/solution architect, **bör du använda Service Bus-köer** när:

* Lösningen måste kunna ta emot meddelanden utan att behöva söka i kön. Med Service Bus, detta kan uppnås genom att använda-longpolling får igen med de TCP-baserade protokoll som stöds av Service Bus.
* Din lösning kräver kön att tillhandahålla en garanterad först-in-först-ut (FIFO) sorterade leverans.
* Lösningen måste kunna stödja automatisk identifiering av dubbletter.
* Du vill att programmet att bearbeta meddelanden som parallella tidskrävande dataströmmar (meddelanden som är kopplade till en dataström med hjälp av den [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) egenskap). Varje nod i den konsumerande appen konkurrerar om strömmar, till skillnad från meddelanden i den här modellen. När en dataström har tilldelats en konsumerande nod, undersöka noden tillståndet för stream programtillståndet med transaktioner.
* Din lösning kräver transaktionella beteende och Atomicitet när du skickar eller tar emot flera meddelanden från en kö.
* Ditt program hanterar meddelanden som kan överskrida 64 KB men begränsas inte troligt metoden 256 KB.
* Du hantera ett krav att tillhandahålla en modell för rollbaserad åtkomst till köer och olika rights/behörigheter för sändare och mottagare.
* Storleken på din kö växer inte större än 80 GB.
* Du vill använda AMQP 1.0 standardiserade meddelanden-protokollet. Läs mer om AMQP [översikt för Service Bus AMQP](service-bus-amqp-overview.md).
* Du kan tänka på en eventuell migrering från Köbaserad point-to-point kommunikation med ett meddelande exchange-mönster som möjliggör sömlös integration av ytterligare mottagare (prenumeranter), som tar emot oberoende kopior av vissa eller alla meddelanden som skickas till kön. Det senare refererar till att publicera/prenumerera funktioner internt av Service Bus.
* Din meddelandelösning måste kunna stödja garanti om leverans för ”i de flesta – en gång” utan att behöva du skapa ytterligare infrastrukturkomponenter.
* Du skulle vilja att kunna publicera och använda batchar av meddelanden.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Jämföra Storage-köer och Service Bus-köer
Tabellerna i följande avsnitt är en logisk gruppering av köfunktioner och kan du jämföra, på ett ögonblick, funktioner som ingår i både Azure Storage-köer och Service Bus-köer.

## <a name="foundational-capabilities"></a>Grundläggande funktioner
Det här avsnittet jämför några av de grundläggande funktioner för meddelandeköer som tillhandahålls av Storage-köer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Sorteringen garanti |**Nej** <br/><br>Mer information finns i den första anteckningen i avsnittet ”Mer Information”.</br> |**Ja - först-In-först-ut (FIFO)**<br/><br>(med hjälp av messaging sessioner) |
| Garanti om leverans |**På minst en gång** |**På minst en gång**<br/><br/>**I de flesta – en gång** |
| Stöd för atomisk åtgärd |**Nej** |**Ja**<br/><br/> |
| Ta emot beteende |**Icke-blockerande**<br/><br/>(slutförs omedelbart om inget nytt meddelande hittas) |**Blockerar med och utan tidsgräns**<br/><br/>(erbjuder longpolling eller [”Comet-tekniken”](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Icke-blockerande**<br/><br/>(med hjälp av .NET-hanterade API: et endast) |
| Push-style-API |**Nej** |**Ja**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) och **OnMessage** sessioner .NET API. |
| Ta emot läge |**Peek & lån** |**Peek & Lås**<br/><br/>**Ta emot och ta bort** |
| Exklusivt läge |**Lån-baserad** |**Lås-baserad** |
| / Utlämningslås varaktighet |**30 sekunder (standard)**<br/><br/>**7 dagar (max)** (du kan förnya eller frigöra en meddelande lån med hjälp av den [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API.) |**60 sekunder (standard)**<br/><br/>Du kan förnya ett meddelande Lås med den [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| / Utlämningslås precision |**Meddelandenivå**<br/><br/>(varje meddelande kan ha ett annat timeout-värde som du kan sedan uppdatera efter behov när behandlingen av meddelandet med hjälp av den [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API) |**Kö-nivå**<br/><br/>(varje kö har en Lås-precision som tillämpas på alla dess meddelanden, men du kan förnya låset med hjälp av den [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Batchar får |**Ja**<br/><br/>(uttryckligen anger antal meddelanden vid hämtning av meddelanden, upp till maximalt 32 meddelanden) |**Ja**<br/><br/>(implicit aktiverar en före fetch-egenskapen eller uttryckligen med hjälp av transaktioner) |
| Satsvis skicka |**Nej** |**Ja**<br/><br/>(med hjälp av transaktioner eller klientsidan batchbearbetning) |

### <a name="additional-information"></a>Ytterligare information
* Meddelanden i Storage-köer är vanligtvis först-in-först-ut, men de kan ibland vara ur funktion, till exempel när ett meddelande synlighet tidslängden upphör att gälla (till exempel till följd av ett klientprogram som kraschar under bearbetning). När synlighet tidsgränsen upphör blir meddelandet synligt igen i kön för en annan worker att ta bort från kön den. I det här läget kan nyligen visas meddelandet placeras i kö (för att vara tagits bort från kön igen) efter ett meddelande som ursprungligen i kön efter den.
* Garanterad FIFO-mönstret i Service Bus-köer måste du använda meddelanden sessioner. I händelse av att programmet kraschar vid bearbetning av ett meddelande tas emot i den **Granska & låsa** läge, nästa gång en kö mottagare tar emot en meddelandesession, den börjar med misslyckade meddelandet efter dess time to live (TTL) period har löpt ut.
* Storage-köer är utformade för att stödja standard queuing scenarier, t.ex. Frikoppling programkomponenter att öka skalbarheten och tolerans för fel, läsa in Utjämning och utveckling av processarbetsflöden.
* Stöd för Service Bus-köer i *på minst en gång* garanti om leverans. Dessutom kan den *på de flesta – en gång* semantiska kan stödjas genom att använda sessionstillstånd för att lagra programtillståndet och använda transaktioner atomiskt ta emot meddelanden och uppdatera sessionens tillstånd.
* Storage-köer ger en enhetlig och konsekvent programmeringsmodell på köer, tabeller och Blobbar – både för utvecklare och för driftteam.
* Service Bus-köer ger stöd för lokala transaktioner i kontexten för en enskild kö.
* Den **ta emot och ta bort** läge som stöds av Service Bus gör möjligheten att minska antal meddelanden (och associerade kostnader) i utbyte mot sänkt leverans assurance.
* I lagringsköer finns lån med möjlighet att utöka lån för meddelanden. På så sätt kan arbetare att underhålla korta lån på meddelanden. Därför om en arbetsroll kraschar kan meddelandet snabbt bearbetas igen som en annan arbetsprocess. Dessutom kan en worker förlänga lånet på ett meddelande om det behöver bearbeta den längre än den aktuella tiden för lånet.
* Storage-köer erbjuder en synlighetstimeout som du kan ange vid enqueuing eller mellan köer för ett meddelande. Du kan också uppdatera ett meddelande med olika lånet värden vid körning och uppdatera olika värden över meddelanden i samma kö. Service Bus lås-timeout har definierats i kö metadata; men du kan förnya låset genom att anropa den [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metod.
* Den maximala tidsgränsen är för en blockerar mottagningsåtgärd i Service Bus-köer 24 dagar. REST-baserad tidsgränser har dock ett maxvärde 55 sekunder.
* Klientsidan batchbearbetning tillhandahålls av Service Bus gör att klienter kön kan batch-flera meddelanden till en enda skicka-åtgärd. Batchbearbetning är endast tillgängligt för skicka asynkrona åtgärder.
* Funktioner, till exempel 200 TB taket lagringsköer (mer när du virtualisera konton) och ett obegränsat antal köer gör det en utmärkt plattform för SaaS-leverantörer.
* Storage-köer ger en flexibel och högpresterande delegerad åtkomstkontroll.

## <a name="advanced-capabilities"></a>Avancerade funktioner
Det här avsnittet jämför avancerade funktioner som tillhandahålls av Storage-köer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Schemalagd leverans |**Ja** |**Ja** |
| Automatisk obeställbara |**Nej** |**Ja** |
| Öka värdet för kön time-to-live |**Ja**<br/><br/>(via plats-uppdatering av synlighetstimeout) |**Ja**<br/><br/>(tillhandahålls via en dedikerad API-funktion) |
| Skadliga meddelande support |**Ja** |**Ja** |
| Uppdatering på plats |**Ja** |**Ja** |
| Transaktionsloggen för serversidan |**Ja** |**Nej** |
| Storage-mått |**Ja**<br/><br/>**Minut mått**: innehåller realtidsstatistisk för API för tillgänglighet, TPS, antal, antal fel med mera, allt i realtid (samman per minut och rapporterats inom några minuter från vad hände bara i produktion. Mer information finns i [om Storage Analytics mätvärden](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ja**<br/><br/>(bulk frågor genom att anropa [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Tillståndshantering |**Nej** |**Ja**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatisk vidarebefordring av meddelande |**Nej** |**Ja** |
| Rensa kön funktion |**Ja** |**Nej** |
| Meddelande-grupper |**Nej** |**Ja**<br/><br/>(med hjälp av messaging sessioner) |
| Programtillstånd per meddelande grupp |**Nej** |**Ja** |
| Dubblettidentifiering |**Nej** |**Ja**<br/><br/>(kan konfigureras på avsändarsidan) |
| Bläddra meddelande grupper |**Nej** |**Ja** |
| Hämtar meddelandesessioner efter ID |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Båda teknikerna för meddelandeköer kan ett meddelande som ska schemaläggas för leverans vid ett senare tillfälle.
* Kön automatisk vidarebefordran kan tusentals olika köer för att skicka meddelanden till en enskild kö, varifrån det mottagande programmet förbrukar meddelandet automatisk vidarebefordring. Du kan använda den här mekanismen för att uppnå säkerhet, Kontrollflöde och isolera lagring mellan varje meddelande-utgivare.
* Storage-köer ger support för att uppdatera meddelandeinnehåll. Du kan använda den här funktionen för att bevara statusinformation och inkrementella statusuppdateringar till meddelandet så att de kan bearbetas från den senaste kända kontrollpunkten, i stället för att börja om från början. Du kan aktivera för samma scenario genom att använda meddelandesessioner med Service Bus-köer. Sessioner kan du spara och hämta programmets Bearbetningsstatus (med hjälp av [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Död oljekategori](service-bus-dead-letter-queues.md), vilket är endast stöds av Service Bus-köer kan vara användbart för att isolera meddelanden som kan inte bearbetas av det mottagande programmet eller när meddelanden kan inte nå sitt mål på grund av en har upphört att gälla (time to live Egenskapen TTL). TTL-värdet anger hur länge ett meddelande finns kvar i kön. Med Service Bus flyttas meddelandet till en särskild kö som kallas $DeadLetterQueue när TTL-perioden upphör.
* Att hitta ”skadliga” meddelanden i Storage-köer, när mellan köer programmet går du igenom den [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) för meddelandet. Om **DequeueCount** är större än ett visst tröskelvärde programmet flyttar meddelandet till en programdefinierad ”” kö för obeställbara.
* Storage-köer kan du få en detaljerad logg över alla transaktioner körs mot kön, som även som aggregerade mätvärden. Båda dessa alternativ är användbara för felsökning och förstå hur programmet använder Storage-köer. De är också användbara för prestandajustering programmet och minska kostnaderna med köer.
* Begreppet ”meddelandesessioner” stöds av Service Bus kan meddelanden som tillhör en viss logisk grupp som ska associeras med en viss mottagare, vilket i sin tur skapar en session-liknande tillhörighet mellan meddelanden och deras respektive mottagare. Du kan aktivera den här avancerade funktioner i Service Bus genom att ange den [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) egenskap på ett meddelande. Mottagare kan lyssna på en specifik sessions-ID och ta emot meddelanden som delar angivna sessions-ID.
* Duplicering identifiering av funktioner som stöds av Service Bus-köer automatiskt tar bort dubbletter av meddelanden skickas till en kö eller ämne, baserat på värdet för den [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) egenskapen.

## <a name="capacity-and-quotas"></a>Kapacitet och kvoter
Det här avsnittet jämför Storage-köer och Service Bus-köer ur [kapacitet och kvoter](service-bus-quotas.md) som kan tillkomma.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Maximal köstorlek |**500 TB**<br/><br/>(begränsat till en [enkel kapacitet för lagringskonton](../storage/common/storage-introduction.md#queue-storage)) |**1 GB till 80 GB**<br/><br/>(definieras när den har skapats av en kö och [aktiverar partitionering](service-bus-partitioning.md) – finns i avsnittet ”Mer Information”) |
| Största meddelandestorlek |**64 KB**<br/><br/>(48 KB när du använder **Base64** kodning)<br/><br/>Azure har stöd för stora meddelanden genom att kombinera köer och blobbar – då du kan sätta upp till 200 GB för ett enskilt objekt. |**256 KB** eller **1 MB**<br/><br/>(inklusive både rubriken och brödtexten, maximalt huvudstorlek: 64 KB).<br/><br/>Beror på den [tjänstnivå](service-bus-premium-messaging.md). |
| Maximal meddelande TTL |**Oändlig** (från och med api-version 2017-07-27) |**TimeSpan.Max** |
| Maximalt antal köer |**Obegränsat** |**10,000**<br/><br/>(per namnområde för tjänsten) |
| Högsta antal samtidiga klienter |**Obegränsat** |**Obegränsat**<br/><br/>(100 samtidiga anslutningsgräns gäller endast för TCP-protokollsbaserade-kommunikation) |

### <a name="additional-information"></a>Ytterligare information
* Service Bus framtvingar storleksbegränsningar för kön. Maximal köstorlek har angetts vid skapande av kön och kan ha ett värde mellan 1 och 80 GB. Om storleksvärdet kö på att skapa kön har uppnåtts kan ytterligare inkommande meddelanden kommer att avvisas och ett undantag som tas emot av den anropande koden. Mer information om kvoter i Service Bus finns i [Service Bus-kvoter](service-bus-quotas.md).
* Partitionering stöds inte i den [premiumnivån](service-bus-premium-messaging.md). Du kan skapa Service Bus-köer i 1, 2, 3, 4 eller 5 GB storlekar (standardvärdet är 1 GB) i Standard-nivån. I Standard-nivån med partitionering aktiverad (vilket är standard), Service Bus skapar 16 partitioner för varje GB som du anger. Därmed, om du skapar en kö som är 5 GB i storlek med 16 partitioner största köstorlek blir (5 * 16) = 80 GB. Du kan se den maximala storleken för din partitionerad kö eller ämne genom att titta på posten den [Azure-portalen][Azure portal].
* Med Storage-köer om meddelandets innehåll inte är XML-safe måste den vara **Base64** kodad. Om du **Base64**-koda meddelandet, användaren nyttolasten kan vara upp till 48 KB, i stället för 64 KB.
* Med Service Bus-köer, varje meddelande som lagras i en kö består av två delar: en rubrik och en brödtext. Den totala storleken på meddelandet får inte överskrida den maximala meddelandestorleken som stöds av tjänstnivån.
* När klienterna kommunicerar med Service Bus-köer via TCP-protokollet, är det maximala antalet samtidiga anslutningar till en enda Service Bus-kö begränsad till 100. Det här talet delas mellan avsändarna och mottagarna. Om den här kvoten har uppnåtts kan efterföljande begäranden om ytterligare anslutningar kommer att avvisas och ett undantag som tas emot av den anropande koden. Den här gränsen har inte införts på klienter som ansluter till köer med hjälp av REST-baserad API.
* Om du behöver fler än 10 000 köer i ett enda Service Bus-namnområde kan du kontakta Azure-supporten och begära en ökning. Om du vill skala utöver 10 000 köer med Service Bus, kan du också skapa ytterligare namnområden med hjälp av den [Azure-portalen][Azure portal].

## <a name="management-and-operations"></a>Hantering och åtgärder
Det här avsnittet jämför hanteringsfunktionerna som tillhandahålls av Storage-köer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Management-protokollet |**REST-via HTTP/HTTPS** |**REST-över HTTPS** |
| Runtime-protokollet |**REST-via HTTP/HTTPS** |**REST-över HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP med TLS)** |
| .NET-API |**Ja**<br/><br/>(.NET Lagringsklient-API) |**Ja**<br/><br/>(.NET Service Bus API) |
| Native C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP API |**Ja** |**Ja** |
| Node.js API |**Ja** |**Ja** |
| Stöd för valfria metadata |**Ja** |**Nej** |
| Regler för namngivning av kön |**Upp till 63 tecken**<br/><br/>(Bokstäver i ett könamn måste vara versaler.) |**Upp till 260 tecken**<br/><br/>(Kö sökvägar och namn är skiftlägesokänsliga.) |
| Hämta kö längd funktionen |**Ja**<br/><br/>(Ungefärligt värde om meddelanden förfaller utanför TTL-Perioden utan tas bort.) |**Ja**<br/><br/>(Exakta, point-in-time-värde.) |
| Peek-funktion |**Ja** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Storage-köer ger stöd för valfria attribut som kan tillämpas på köbeskrivningen, i form av namn/värde-par.
* Båda teknikerna för kön erbjuder möjligheten att granska ett meddelande utan att låsa den, vilket kan vara användbart när du implementerar en kö webbläsaren och explorer-verktyget.
* Service Bus .NET asynkrona meddelanden API: er utnyttjar full duplex TCP-anslutningar för bättre prestanda jämfört med REST över HTTP och stöd för AMQP 1.0-standardprotokollet.
* Namnen på Storage-köer kan vara 3 – 63 tecken långt, kan innehålla gemena bokstäver, siffror och bindestreck. Mer information finns i [namngivning av köer och Metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus könamn kan vara upp till 260 tecken och har mindre restriktivt namngivningsregler. Service Bus könamn får innehålla bokstäver, siffror, punkter, bindestreck och understreck.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det här avsnittet beskrivs autentisering och auktorisering funktioner som stöds av Storage-köer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Autentisering |**Symmetrisk nyckel** |**Symmetrisk nyckel** |
| Säkerhetsmodell |Delegerad åtkomst via SAS-token. |SAS |
| Provider för identitetsfederation |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Varje begäran till någon av de Meddelandeköer teknikerna måste autentiseras. Offentliga köer med anonym åtkomst stöds inte. Med hjälp av [SAS](service-bus-sas.md), du kan lösa det här scenariot genom att publicera en lässkyddad SAS, skrivskyddad SAS eller även en fullständig åtkomst SAS.
* Schema för autentiseringsmetoder som anges av Storage köer innebär användning av en symmetrisk nyckel, vilket är en hashbaserad meddelandeautentiseringskod (HMAC), beräknas med SHA-256-algoritmen och kodat som en **Base64** sträng. Mer information om respektive-protokollet finns i [autentisering för Azure Storage-tjänster](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-köer stöder en liknande modell med symmetriska nycklar. Mer information finns i [signatur autentisering för delad åtkomst med Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Sammanfattning
Du kan göra ett mer välgrundade beslut om vilken kö-teknik för att använda, genom att få en djupare förståelse för de två teknikerna, och när. Beslutet om när du ska använda Storage-köer eller Service Bus-köer tydligt beror på ett antal faktorer. De här faktorerna kan kraftigt beroende individuella behov av ditt program och dess arkitektur. Om programmet redan använder de viktigaste funktionerna i Microsoft Azure kan överväga du att välja Storage-köer, särskilt om du kräver grundläggande kommunikation och skickar meddelanden mellan tjänster eller behöver köer som kan vara större än 80 GB i storlek.

Eftersom Service Bus-köer ger ett antal avancerade funktioner, till exempel sessioner, transaktioner, dubblettidentifiering, automatisk dead-lettering och varaktiga Publicera/prenumerera på funktioner, de kan vara ett önskade alternativ om du skapar en hybrid programmet eller om programmet annars kräver dessa funktioner.

## <a name="next-steps"></a>Nästa steg
Följande artiklar innehåller mer vägledning och information om hur du använder Storage-köer eller Service Bus-köer.

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Hur du använder Queue Storage-tjänsten](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Metodtips för prestandaförbättringar med hjälp av Service Bus brokered messaging](service-bus-performance-improvements.md)
* [Introduktion till köer och ämnen i Azure Service Bus (blogginlägg)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Utvecklarens Guide till Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Med kötjänsten i Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

