---
title: "Azure Storage-köer och Service Bus-köer jämfört med och skillnad från | Microsoft Docs"
description: "Analyserar likheter mellan två typer av köer som erbjuds av Azure och."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/08/2017
ms.author: sethm
ms.openlocfilehash: f13c7330c9e828abe6557149b9a31c7170e33dcd
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage-köer och Service Bus-köer - skillnad från och med
Den här artikeln analyserar skillnader och likheter mellan de två typerna av köer som erbjuds av Microsoft Azure idag: lagringsköer och Service Bus-köer. Med hjälp av informationen kan du jämföra de olika teknikerna och fatta klokare beslut när du ska avgöra vilken lösning som passar dig bäst.

## <a name="introduction"></a>Introduktion
Azure stöder två typer av kön mekanismer: **lagringsköer** och **Service Bus-köer**.

**Lagringsköer**, som är en del av den [Azure storage](https://azure.microsoft.com/services/storage/) infrastruktur, funktionen ett enkelt gränssnitt i REST-baserad GET/PUT/TITT att tillhandahålla tillförlitlig och beständiga messaging inom och mellan tjänster.

**Service Bus-köer** är en del av en bredare [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastruktur som stöder queuing samt publicera/prenumerera och mer avancerade integration mönster. Mer information om Service Bus-köer/artiklar/prenumerationer finns i [översikt över Service Bus](service-bus-messaging-overview.md).

Medan båda queuing teknikerna samtidigt kan introducerades lagringsköer först som en mekanism för lagring av dedikerade kön byggda på Azure Storage-tjänster. Service Bus-köer är byggda på bredare meddelandeinfrastrukturen integreras program eller programkomponenter som kan sträcka sig över flera kommunikationsprotokoll, datakontrakt, betrodda domäner och nätverksmiljöer.

## <a name="technology-selection-considerations"></a>Överväganden för val av teknik
Både lagringsköer och Service Bus-köer är implementeringar av message queuing-tjänsten som erbjuds av Microsoft Azure. Varje har ett något annorlunda funktionsuppsättningen, vilket innebär att du kan välja en av eller båda, beroende på din lösning eller business-tekniska problem du lösa behov.

När du fastställer vilka queuing teknik passar syftet med en viss lösning bör lösningsarkitekter och utvecklare de här rekommendationerna. Mer information finns i nästa avsnitt.

Som lösning systemarkitekt/utvecklare, **bör du använda lagringsköer** när:

* Programmet måste lagra över 80 GB meddelanden i en kö, där meddelandena som har en livslängd som är kortare än sju dagar.
* Programmet vill följa förloppet för bearbetning av ett meddelande i kön. Detta är användbart om arbetaren bearbetning av ett meddelande kraschar. En efterföljande arbetare kan sedan använda informationen för att fortsätta från där den tidigare worker slutade.
* Du behöver sida serverloggen för alla transaktioner som körs mot din köer.

Som lösning systemarkitekt/utvecklare, **bör du använda Service Bus-köer** när:

* Din lösning kunna ta emot meddelanden utan att behöva söka kön. Med Service Bus kan göra detta genom att använda den långa avsökningen mottagningsåtgärd med hjälp av TCP-baserat protokoll som stöds i Service Bus.
* Lösningen kräver kön att tillhandahålla en garanterad första-i-first out (FIFO) sorterade leverans.
* Vill du en symmetrisk upplevelse i Azure och Windows Server (privat moln). Mer information finns i [Service Bus för Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).
* Din lösning kunna stödja automatisk identifiering av dubbletter.
* Du vill att programmet för att bearbeta meddelanden som parallella tidskrävande dataströmmar (meddelanden som är kopplad till en dataström med hjälp av den [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) -egenskap). Varje nod i den konsumerande appen konkurrerar om strömmar, till skillnad från meddelanden i den här modellen. När en ström som har tilldelats en lång nod, kan noden Kontrollera status för dataströmmen programtillståndet använder transaktioner.
* Lösningen kräver transaktionella beteende och gör att skicka eller ta emot flera meddelanden från en kö.
* Time to live (TTL) egenskap för programspecifika arbetsbelastningen kan överskrida den 7 dagar.
* Programmet hanterar meddelanden som kan överskrida 64 KB men begränsas inte troligt metoden 256 KB.
* Du hantera ett krav att tillhandahålla en modell för rollbaserad åtkomst till köer och olika rights/behörigheter för avsändare och mottagare.
* Din köstorlek växer inte större än 80 GB.
* Du vill använda AMQP 1.0 standardbaserad messaging-protokollet. Läs mer om AMQP [översikt över Service Bus AMQP](service-bus-amqp-overview.md).
* Du kan föreställa sig en eventuell migrering från kön-baserad Point-to-point-kommunikation med ett meddelande exchange mönster som möjliggör sömlös integration av ytterligare mottagare (prenumeranter), som tar emot oberoende kopior av vissa eller alla meddelanden som skickas till kön. Dessa avser Publicera/prenumerera funktioner internt av Service Bus.
* Din meddelandelösning måste kunna stödja ”i de flesta – en gång” leverans garanti utan att behöva skapa ytterligare infrastrukturkomponenter.
* Du vill kunna publicera och använda batchar av meddelanden.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Jämföra lagringsköer och Service Bus-köer
Tabellerna i följande avsnitt innehåller en logisk gruppering av funktioner i kön och gör att du snabbt kan jämföra funktionerna som är tillgängliga i både Azure Storage-köer och Service Bus-köer.

## <a name="foundational-capabilities"></a>Grundläggande funktioner
Det här avsnittet jämför några av de grundläggande queuing funktioner som tillhandahålls av lagringsköer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Ordning garanti |**Nej** <br/><br>Mer information finns i den första anteckningen i avsnittet ”Mer Information”.</br> |**Ja - First i First Out (FIFO)**<br/><br>(genom att använda messaging sessioner) |
| Leverans av säkerhet |**På-minst en gång** |**På-minst en gång**<br/><br/>**I de flesta – en gång** |
| Stöd för atomisk åtgärd |**Nej** |**Ja**<br/><br/> |
| Ta emot beteende |**Icke-blockerande**<br/><br/>(har slutförts omedelbart om inga nya meddelanden) |**Blockerar med och utan timeout**<br/><br/>(erbjuder länge avsökning eller [”Comet tekniken”](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Icke-blockerande**<br/><br/>(genom att använda .NET hanterad API endast) |
| API för push-format |**Nej** |**Ja**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) och **OnMessage** sessioner .NET-API. |
| Mottagningsläge |**Granska & låna ut** |**Granska & Lås**<br/><br/>**Ta bort & ta emot** |
| Exklusivt läge |**Lease-baserade** |**Lås-baserade** |
| / Utlämningslås varaktighet |**30 sekunder (standard)**<br/><br/>**7 dagar (max)** (du kan förnya eller släppa ett meddelande lån med hjälp av den [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API.) |**60 sekunder (standard)**<br/><br/>Du kan förnya ett meddelande Lås med den [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| / Utlämningslås precision |**Meddelandenivå**<br/><br/>(varje meddelande kan ha ett annat timeout-värde som du kan sedan uppdatera som behövs vid bearbetningen av meddelandet med hjälp av den [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API) |**Kön nivå**<br/><br/>(varje kö har ett lås precision som används för alla meddelanden, men du kan förnya Lås med hjälp av den [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Batchar ta emot |**Ja**<br/><br/>(explicit anger antalet meddelanden vid hämtning av meddelanden, upp till högst 32 meddelanden) |**Ja**<br/><br/>(aktivera implicit en före fetch-egenskap eller uttryckligen med hjälp av transaktioner) |
| Batch skicka |**Nej** |**Ja**<br/><br/>(genom att använda transaktioner eller batchbearbetning på klientsidan) |

### <a name="additional-information"></a>Ytterligare information
* Meddelanden i lagringsköer är vanligtvis första-i-first out, men de kan ibland vara oordning; till exempel när ett meddelande visas timeout-varaktighet upphör att gälla (t.ex, på grund av ett klientprogram kraschar under bearbetning). När tidsgränsen för visning upphör att gälla blir meddelandet visas igen på kön för en annan worker att det har status Created. Då kan nyligen visas meddelandet placeras i kön (för att vara togs bort från kön igen) efter ett meddelande som ursprungligen köas efter den.
* Garanterad FIFO-mönstret i Service Bus-köer kräver användning av messaging-sessioner. I händelse av att programmet kraschar vid bearbetning av ett meddelande som togs emot i den **Granska & Lås** läge nästa gång en kö mottagare accepterar en meddelanden session startas med misslyckade meddelandet efter dess time to live (TTL) period har löpt ut.
* Lagringsköer är designade att stöda standard queuing scenarier, t.ex. Frikoppling programkomponenter att öka skalbarheten och tolerans för fel, läsa in Utjämning och utveckling av processarbetsflöden.
* Stöd för Service Bus-köer i *på-minst en gång* leverans av garanti. Dessutom kan den *på de flesta – en gång* semantiska kan användas med hjälp av sessionens tillstånd för att lagra programtillståndet och använder transaktioner för att automatiskt ta emot meddelanden och uppdatera sessionens tillstånd.
* Lagringsköer ger en enhetlig och konsekvent programmeringsmodell över köer, tabeller och Blobbar – både för utvecklare och team.
* Service Bus-köer ger stöd för lokala transaktioner i kontexten för en enskild kö.
* Den **ta emot och ta bort** läge som stöds av Service Bus ger dig möjlighet att minska den asynkrona åtgärden antal (och associerade kostnaden) mot nedsänkt leverans säkerhet.
* Lagringsköer ge lån möjligheter att utöka lån för meddelanden. Detta gör att anställda att underhålla kort lån på meddelanden. Därför om en worker kraschar kan meddelandet snabbt bearbetas igen med en annan worker. Dessutom kan en arbetare förlänga lånet på ett meddelande om behöver bearbetar den längre än den aktuella lånetiden.
* Lagringsköer erbjuder en synlighet tidsgräns som du kan ange när enqueuing eller mellan köer för ett meddelande. Dessutom kan du uppdatera ett meddelande med olika lån värden vid körning och uppdatera olika värden över meddelanden i samma kö. Service Bus lås-timeout har definierats i kön metadata; men du kan förnya låset genom att anropa den [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metod.
* Maximal timeout-värdet är för en spärr mottagningsåtgärd i Service Bus-köer 24 dagar. REST-baserad timeout har dock ett maximalt värde 55 sekunder.
* Klientsidan batchbearbetning tillhandahålls av Service Bus gör en kö klienten att batchbearbeta flera meddelanden till en enda send-åtgärd. Batchbearbetning är endast tillgängligt för asynkron sändning åtgärder.
* Funktioner, till exempel 200 TB taket lagringsköer (Mer information när du virtualisera konton) och obegränsade köer gör det en utmärkt plattform för SaaS-providers.
* Storage-köer ger en flexibel och performant delegerad åtkomstkontroll.

## <a name="advanced-capabilities"></a>Avancerade funktioner
Det här avsnittet jämför avancerade funktioner som tillhandahålls av lagringsköer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Schemalagd leverans |**Ja** |**Ja** |
| Automatisk döda lettering |**Nej** |**Ja** |
| Öka kön time to live-värde |**Ja**<br/><br/>(via uppdatering på plats av synlighet tidsgräns) |**Ja**<br/><br/>(som tillhandahålls via en dedikerad API-funktion) |
| Skadligt meddelande stöd |**Ja** |**Ja** |
| Uppdatering på plats |**Ja** |**Ja** |
| Transaktionsloggen för serversidan |**Ja** |**Nej** |
| Storage-mätvärden |**Ja**<br/><br/>**Minuters mått**: innehåller realtid mått för tillgänglighet, Transaktionsprogram, API antal fel antal och mer i realtid (sammanställs per minut och rapporterats inom några minuter från vad hände bara i produktion. Mer information finns i [om Storage Analytics mätvärden](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ja**<br/><br/>(Massredigera frågor genom att anropa [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Hantering av tillstånd |**Nej** |**Ja**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus.active), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus.disabled), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus.senddisabled), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus.receivedisabled) |
| Automatisk vidarebefordring av meddelande |**Nej** |**Ja** |
| Rensa kön funktion |**Ja** |**Nej** |
| Meddelande-grupper |**Nej** |**Ja**<br/><br/>(genom att använda messaging sessioner) |
| Programtillstånd per meddelande grupp |**Nej** |**Ja** |
| Identifiering av dubbletter |**Nej** |**Ja**<br/><br/>(kan konfigureras på avsändaren sida) |
| Bläddra meddelandet grupper |**Nej** |**Ja** |
| Hämtar sessioner för meddelande-ID: t |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Båda teknikerna för meddelandeköer kan ett meddelande som ska schemaläggas för leverans av vid ett senare tillfälle.
* Kön automatisk vidarebefordring kan tusentals köer om du vill skicka meddelanden till en enskild kö som det mottagande programmet förbrukar meddelandet automatisk vidarebefordring. Du kan använda denna mekanism för att uppnå säkerhet, styr flödet och isolera lagring mellan varje meddelande utgivare.
* Lagringsköer ger stöd för att uppdatera innehållet. Du kan använda den här funktionen för bestående statusinformation och inkrementell statusuppdateringar i meddelandet, så att den kan bearbetas från den senaste kända kontrollpunkten från grunden. Du kan aktivera samma scenario genom att använda meddelandet sessioner med Service Bus-köer. Sessioner kan du spara och hämta bearbetning programtillståndet (med hjälp av [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Döda oljekategori](service-bus-dead-letter-queues.md), vilket är endast stöds av Service Bus-köer kan vara användbart för att isolera meddelanden som kan inte bearbetas av det mottagande programmet eller när meddelanden som inte går att nå sina mål på grund av ett har upphört att gälla (time to live Egenskapen TTL). TTL-värdet anger hur länge ett meddelande som finns kvar i kön. Med Service Bus flyttas meddelandet till en särskild kö anropas $DeadLetterQueue när TTL-period har löpt ut.
* Att hitta ”förgiftade” meddelanden i lagringsköer, när mellan köer programmet undersöker den [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) för meddelandet. Om **DequeueCount** är större än ett visst tröskelvärde programmet flyttas meddelandet till en kö programdefinierade ”obeställbara”.
* Lagringsköer kan du få en detaljerad logg över alla transaktioner körs mot kön, som också som aggregerade mått. Båda dessa alternativ är användbara för felsökning och förstå hur programmet använder lagringsköer. De är också användbara för prestandajustering ditt program och minska kostnaderna för med hjälp av köer.
* Begreppet ”delta” stöds av Service Bus gör att meddelanden som tillhör en viss logisk grupp som ska associeras med en viss mottagare, vilket i sin tur skapar en session-liknande mappning mellan meddelanden och deras respektive mottagare. Du kan aktivera det avancerade funktioner i Service Bus genom att ange den [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) egenskapen på ett meddelande. Mottagare kan lyssna på en specifik sessions-ID och ta emot meddelanden som delar angivna sessions-ID.
* Duplicering identifiering funktioner som stöds av Service Bus-köer automatiskt tar du bort dubbla meddelanden som skickas till en kö eller ett ämne, baserat på värdet för den [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) egenskapen.

## <a name="capacity-and-quotas"></a>Kapacitet och kvoter
Det här avsnittet jämför lagringsköer och Service Bus-köer ur [kapacitet och kvoter](service-bus-quotas.md) som kan tillkomma.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Största köstorlek |**500 TB**<br/><br/>(begränsat till en [enkel kapacitet för lagringskonton](../storage/common/storage-introduction.md#queue-storage)) |**1 GB till 80 GB**<br/><br/>(definieras när du skapar en kö och [aktiverar partitionering](service-bus-partitioning.md) – finns i avsnittet ”Mer Information”) |
| Maximal meddelandestorlek |**64 KB**<br/><br/>(48 KB när du använder **Base64** kodning)<br/><br/>Azure stöder stora meddelanden genom att kombinera köer och blobbar – nu kan du sätta upp till 200 GB för ett enskilt objekt. |**256 KB** eller **1 MB**<br/><br/>(inklusive både sidhuvud och brödtext, högsta huvudstorlek: 64 KB).<br/><br/>Beror på den [tjänstnivån](service-bus-premium-messaging.md). |
| Maximal meddelande-TTL |**7 dagar** |**TimeSpan.Max** |
| Maximalt antal köer |**Obegränsat** |**10,000**<br/><br/>(per namnområde för tjänsten) |
| Maximalt antal samtidiga klienter |**Obegränsat** |**Obegränsat**<br/><br/>(100 samtidiga anslutningsgränsen gäller endast för TCP-protokoll-baserad kommunikation) |

### <a name="additional-information"></a>Ytterligare information
* Service Bus tillämpar storleksbegränsningar för kön. Största köstorlek har angetts vid skapande av kön och kan ha ett värde mellan 1 och 80 GB. Om storleksvärdet kö på skapande av kön uppnås ytterligare inkommande meddelanden avvisas och tas emot ett undantag av den anropande koden. Mer information om kvoter i Service Bus finns [Service Bus kvoter](service-bus-quotas.md).
* I den [standardnivån](service-bus-premium-messaging.md), kan du skapa Service Bus-köer i 1, 2, 3, 4 och 5 GB storlek (standard är 1 GB). Du kan skapa köer i Premium-nivån upp till 80 GB i storlek. I Standard nivå med partitionering är aktiverad (vilket är standard), Service Bus skapar 16 partitioner för varje GB som du anger. Så om du skapar en kö är 5 GB i storlek med 16 partitioner största köstorlek blir (5 * 16) = 80 GB. Du kan se den maximala storleken på din partitionerade kö eller ett ämne genom att titta på posten den [Azure-portalen][Azure portal]. Premium-nivån skapas endast 2 partitioner per kö.
* Med lagringsköer, om innehållet i meddelandet inte är XML-kassaskåp, sedan måste det vara **Base64** kodad. Om du **Base64**-koda meddelandet, nyttolasten användare kan vara upp till 48 KB, i stället för 64 KB.
* Med Service Bus-köer, varje meddelande som lagras i en kö består av två delar: en rubrik och text. Den totala storleken på meddelandet får inte överskrida den maximala storleken som stöds av tjänstnivån.
* När klienterna kommunicerar med Service Bus-köer via TCP-protokollet, är det maximala antalet samtidiga anslutningar till en enskild Service Bus-kö begränsad till 100. Det här antalet delas mellan avsändare och mottagare. Om den här kvoten har uppnåtts efterföljande begäranden om ytterligare anslutningar avvisas och tas emot ett undantag av den anropande koden. Den här begränsningar inte på klienter som ansluter till köer med hjälp av REST-baserad API.
* Om du behöver fler än 10 000 köer i en enda Service Bus-namnrymd kan du begära en Kontakta Azure supportteamet. Om du vill skala upp 10 000 köer med Service Bus, kan du också skapa ytterligare namnområden med hjälp av den [Azure-portalen][Azure portal].

## <a name="management-and-operations"></a>Hantering och åtgärder
Det här avsnittet jämför hanteringsfunktionerna som tillhandahålls av lagringsköer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Management-protokollet |**REST-via HTTP/HTTPS** |**PLACERAR över HTTPS** |
| Runtime-protokollet |**REST-via HTTP/HTTPS** |**PLACERAR över HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP med TLS)** |
| .NET-API |**Ja**<br/><br/>(.NET lagring klient API) |**Ja**<br/><br/>.NET Service Bus (API) |
| Interna C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP-API |**Ja** |**Ja** |
| Node.js API |**Ja** |**Ja** |
| Stöd för godtyckliga metadata |**Ja** |**Nej** |
| Kön namngivningsregler |**Upp till 63 tecken**<br/><br/>(Bokstäver i en kö måste vara gemener.) |**Högst 260 tecken**<br/><br/>(Kön sökvägar och namn är skiftlägeskänsliga.) |
| Hämta kön längd funktion |**Ja**<br/><br/>(Ungefärliga värdet om meddelanden ut utanför TTL-Perioden utan att tas bort.) |**Ja**<br/><br/>(Exakt, point-in-time-värde.) |
| Granska funktionen |**Ja** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Lagringsköer ger stöd för valfria attribut som kan användas för kö beskrivning, i form av namn/värde-par.
* Båda teknikerna kön har möjlighet att läsa ett meddelande utan att låsa den, vilket kan vara användbart när du implementerar en kö webbläsaren och explorer-verktyget.
* Service Bus .NET asynkrona meddelanden API: er utnyttjar full duplex TCP-anslutningar för bättre prestanda jämfört med REST över HTTP och stöder AMQP 1.0-standardprotokollet.
* Namnen på lagringsköer kan vara 3-63 tecken långt, får innehålla gemena bokstäver, siffror och bindestreck. Mer information finns i [namngivning av köer och Metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus könamn kan vara högst 260 tecken och har mindre begränsande namngivningsregler. Service Bus könamn kan innehålla bokstäver, siffror, punkter, bindestreck och understreck.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det här avsnittet beskrivs de autentisering och auktorisering funktioner som stöds av lagringsköer och Service Bus-köer.

| Jämförelsevillkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Autentisering |**Symmetrisk nyckel** |**Symmetrisk nyckel** |
| Säkerhetsmodell |Delegerad åtkomst via SAS-token. |SAS |
| Provider för identitetsfederation |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Alla begäranden om något av de Meddelandeköer teknikerna måste autentiseras. Offentliga köer med anonym åtkomst stöds inte. Med hjälp av [SAS](service-bus-sas.md), du kan åtgärda det här scenariot genom att publicera en lässkyddad SAS, skrivskyddad SAS eller även en full åtkomst SAS.
* Autentiseringsschema som anges av lagring köer innebär användning av en symmetrisk nyckel, vilket är en hashbaserad meddelandeautentiseringskod (HMAC), beräknas med SHA-256-algoritmen och kodad som en **Base64** sträng. Mer information om respektive-protokollet finns [autentisering för Azure Storage-tjänster](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-köer stöder en liknande modell med symmetriska nycklar. Mer information finns i [signatur autentisering för delad åtkomst med Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Slutsats
Få en djupare förståelse för de två teknikerna du ska kunna göra välgrundade beslut om vilken kö-teknik som ska användas, och när. Beslutet om när du ska använda Service Bus-köer eller lagringsköer tydligt beror på ett antal faktorer. Dessa faktorer kan kraftigt beroende programmet och dess arkitektur enskilda behov. Om programmet redan använder grundfunktionerna i Microsoft Azure måste överväga du att välja köer för lagring, särskilt om du kräver grundläggande kommunikation och skickar meddelanden mellan tjänster eller behöver köer som kan vara större än 80 GB i storlek.

Eftersom Service Bus-köer ger ett antal avancerade funktioner, till exempel sessioner, transaktioner, dubblettidentifiering, automatisk dead-lettering och varaktig Publicera/prenumerera funktioner, de kan vara ett önskade alternativ om du skapar en hybrid programmet eller om programmet annars kräver dessa funktioner.

## <a name="next-steps"></a>Nästa steg
Följande artiklar innehåller fler riktlinjer och information om hur du använder Service Bus-köer eller lagringsköer.

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Hur du använder tjänsten Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Metodtips för bättre prestanda med hjälp av Service Bus asynkron meddelandetjänst](service-bus-performance-improvements.md)
* [Introduktion till köer och ämnen i Azure Service Bus (blogginlägg)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Utvecklarhandbok till Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Med MSMQ-tjänsten i Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

