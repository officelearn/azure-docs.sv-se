---
title: "Översikt över funktioner i Azure Event Hubs | Microsoft Docs"
description: "Översikt över och information om Händelsehubbar i Azure-funktioner"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: sethm
ms.openlocfilehash: a74d767d57eb5ce2b3a716f9ba908a451f25f538
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-features-overview"></a>Översikt över Event Hubs funktioner

Händelsehubbar i Azure är en skalbar händelsebearbetning tjänsten som en och bearbetar stora mängder händelser och data med låg latens och hög tillförlitlighet. Se [vad är Händelsehubbar?](event-hubs-what-is-event-hubs.md) för en översikt över tjänsten.

Den här artikeln bygger på informationen i den [översikt](event-hubs-what-is-event-hubs.md), och ger information om Händelsehubbar komponenter och funktioner i technical och implementering.

## <a name="event-publishers"></a>Händelseutfärdare

En enhet som skickar data till en händelsehubb är en händelse tillverkare eller *händelseutfärdare*. Händelseutfärdare kan utfärda händelser med hjälp av HTTPS eller AMQP 1.0. Händelseutfärdare använder en SAS-token (signatur för delad åtkomst) för att identifiera sig mot en händelsehubb och kan ha en unik identitet eller använda en gemensam SAS-token.

### <a name="publishing-an-event"></a>Publicera en händelse

Du kan publicera en händelse via AMQP 1.0 eller HTTPS. Händelsehubbar ger [klientbibliotek och klasser](event-hubs-dotnet-framework-api-overview.md) för att publicera händelser till en händelsehubb från .NET-klienter. För andra körningar och plattformar kan du använda alla AMQP 1.0-klienter, t.ex. [Apache Qpid](http://qpid.apache.org/). Du kan publicera händelser individuellt eller i batchar. En enstaka publikation (en instans av händelsedata ) har en begränsning på 256 KB, oavsett om det är en enskild händelse eller en batch. Publicera händelser som är större än tröskelvärdet resultatet i ett fel. Bästa metoden för en utfärdare är att vara ovetande om partitioner i händelsehubben och att bara ange en *partitionsnyckel* (presenteras i nästa avsnitt) eller sin identitet via sin SAS-token.

Valet att använda AMQP eller HTTPS är specifikt för användningsscenariot. AMQP kräver en beständig dubbelriktad socket och dessutom säkerhet på transportnivå (TLS) eller SSL/TLS. AMQP har högre nätverkskostnader när sessionen initieras, men HTTPS kräver ytterligare SSL-kostnader för varje begäran. AMQP har högre prestanda för frekventa utfärdare.

![Händelsehubbar](./media/event-hubs-features/partition_keys.png)

Event Hubs garanterar att alla händelser som delar samma partitionsnyckelvärde levereras i rätt ordning och till samma partition. Om partitionsnycklar används med utfärdarprinciper måste utfärdarens identitet och partitionsnyckelns värde matcha varandra. Annars uppstår ett fel.

### <a name="publisher-policy"></a>Utgivarprincip

Med händelsehubbar får du granulär kontroll över utgivare via *utgivarprinciper*. Utfärdarprinciper är körningsfunktioner som utformats för att ge stöd för ett stort antal oberoende utfärdare. Med utgivarprinciper använder varje utgivare sin egen unika identifierare vid publicering av händelser på en händelsehubb med hjälp av följande mekanism:

```
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Du behöver inte skapa utgivarnamnen i förväg, men de måste matcha SAS-token som används när du publicerar en händelse för att garantera oberoende utgivaridentiteter. När du använder utgivarprinciper ställs **PartitionKey**-värdet in på utgivarens namn. Dessa värden måste matcha för att fungera korrekt.

## <a name="capture"></a>Capture

[Event Hubs avbilda](event-hubs-capture-overview.md) gör att du kan avbilda strömmande data i Händelsehubbar och spara den på ditt val av en Blob storage-konto eller ett tjänstkonto för Azure Data Lake automatiskt. Du kan aktivera avbildning från Azure-portalen och ange minsta storlek och tidsfönstret för avbildningen. Med Event Hubs avbilda kan ange du egna Azure Blob Storage-konto och en behållare eller ett Azure Data Lake Service-kontot som används för att lagra den insamlade data. Insamlade data skrivs i Apache Avro-formatet.

## <a name="partitions"></a>Partitioner

Event Hubs tillhandahåller meddelandeströmning via ett partitionerat konsumentmönster där varje konsument endast läser en specifik delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen.

En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nya händelser anländer läggs de till i slutet av denna sekvens. En partition kan betraktas som en ”genomförandelogg”.

![Händelsehubbar](./media/event-hubs-features/partition.png)

Händelsehubbar behåller data under en konfigurerad kvarhållningstid som gäller för alla partitioner i hubben. Händelser löper ut enligt ett tidsschema. Det går inte att ta bort dem. Eftersom partitioner är oberoende av varandra och innehåller sina egna sekvenser med data, växer de ofta i olika takt.

![Händelsehubbar](./media/event-hubs-features/multiple_partitions.png)

Antalet partitioner anges när de skapas och måste vara mellan 2 och 32. Eftersom det inte går att ändra antalet partitioner bör du tänka på hur många partitioner som kommer att behövas på längre sikt när du anger antalet partitioner. Partitioner är en mekanism för organisering av data som har att göra med vilken underordnad parallellitet som krävs i de program som används. Antalet partitioner i en händelsehubb är direkt kopplat till antalet samtidiga läsare som du förväntar dig. Du kan öka antalet partitioner till mer än 32 genom att kontakta Event Hubs-teamet.

Du rekommenderas att inte skicka direkt till en partition medan partitioner kan identifieras och kan skickas till direkt. Du kan i stället använda konstruktioner på högre nivå som beskrivs i avsnitten [Händelseutfärdare](#event-publishers) och [Kapacitet](#capacity). 

Partitioner är fyllda med en sekvens av händelsedata som innehåller själva händelsen, en användardefinierad egenskapsuppsättning och metadata, till exempel dess offset, eller position, i partitionen och dess nummer i datasekvensströmmen.

Mer information om partitioner och avvägningen mellan tillgänglighet och tillförlitlighet finns i [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md#partition-key) och i artikeln om [tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Partitionsnyckeln

Du kan organisera data med hjälp av en [partitionsnyckel](event-hubs-programming-guide.md#partition-key) som mappar inkommande händelsedata till specifika partitioner. Partitionsnyckeln är ett värde som avsändaren anger och som skickas till en händelsehubb. Den bearbetas via en statisk hash-funktion som skapar partitionstilldelningen. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen.

Händelseutfärdaren känner bara till sin partitionsnyckel, inte den partition som händelserna publiceras till. Frikopplingen av nyckeln och partitionen gör att avsändaren inte behöver känna till så mycket om bearbetningen nedströms. En identitet per enhet eller en användarunik identitet utgör en bra partitionsnyckel, men andra attribut, till exempel geografi, kan också användas för att gruppera relaterade händelser i en enda partition.

## <a name="sas-tokens"></a>SAS-token

Event Hubs använder *signaturer för delad åtkomst* (SAS, Shared Access Signatures) som är tillgängliga på namnområdes- och händelsehubbnivå. En SAS-token genereras från en SAS-nyckel och är en SHA-hash för en URL som kodats i ett specifikt format. Med hjälp av namnet på nyckeln (principen) och token kan Event Hubs återgenerera hash-värdet och därmed autentisera avsändaren. SAS-token för händelseutfärdare skapas vanligtvis med enbart behörighet för att **skicka** i en specifik händelsehubb. Den här URL-mekanismen med SAS-token är den grund för utfärdaridentifiering som presenterades i principen för utfärdare. Mer information om hur du arbetar med SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Händelsekonsumenter

En enhet som läser händelsedata från en händelsehubb är en *händelsekonsument*. Alla Event Hubs-konsumenter ansluter via AMQP 1.0-sessionen och händelser levereras via sessionen när de blir tillgängliga. Klienten behöver inte söka efter datatillgänglighet.

### <a name="consumer-groups"></a>Konsumentgrupper

Publicerings-/prenumerationsmekanismen för Event Hubs aktiveras via *konsumentgrupper*. En konsumentgrupp är en vy (tillstånd, position eller offset) av en hel händelsehubb. Konsumentgrupper gör det möjligt för flera användningsprogram att vart och ett ha en separat vy över händelseströmmen och att oberoende läsa strömmen i egen takt och med sina egna offset.

Inom en arkitektur för strömbearbetning utgör varje nedströms program en konsumentgrupp. Om du vill skriva händelsedata till långsiktig lagring utgör programmet för att skriva data till lagring en konsumentgrupp. Komplex händelsebearbetning kan sedan utföras av en annan, separat konsumentgrupp. Du får bara åtkomst till en partition via en konsumentgrupp. Det får finnas högst 5 läsare på en partition per konsumentgrupp; men **rekommenderas att det finns endast en aktiv mottagaren på en partition per konsumentgrupp**. Det finns alltid en förinställd konsumentgrupp i en händelsehubb, och du kan skapa upp till 20 konsumentgrupper för en händelsehubb på standardnivå.

Följande är exempel på URI-konventionen för konsumentgrupper:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Följande bild visar strömhanteringsarkitekturen för händelsehubbar:

![Händelsehubbar](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Offsets för strömmar

En *offset* är en händelses position inom en partition. Föreställ dig en offset som en markör på klientsidan. Denna offset är en byte-numrering av händelsen. Med den så kan en händelsekonsument (läsare) ange vid vilken punkt i händelseströmmen som läsningen ska starta. Du kan ange offseten som en tidsstämpel eller ett offset-värde. Konsumenterna ansvarar för att lagra sina egna offset-värden utanför händelsehubbtjänsten. Inom en partition innehåller varje händelse en offset.

![Händelsehubbar](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Kontrollpunkter

*Att skapa kontrollpunkter* är en process genom vilken läsare markerar eller sparar sin position inom en händelsesekvens i en partition. Att skapa kontrollpunkter är konsumentens ansvar och görs för varje partition i en konsumentgrupp. Det här ansvaret innebär att varje läsare i partitionen måste hålla reda på sin nuvarande position i händelseströmmen för varje konsumentgrupp. Läsaren kan sedan informera tjänsten när de anser att dataströmmen är klar.

Om en läsare kopplar från en partition och den sedan återansluts kan han börja läsa vid den kontrollpunkt som tidigare skickades in av den senaste läsaren i den aktuella partitionen inom just den konsumentgruppen. När läsaren ansluter skickar partitionen denna offset till händelsehubben för att ange den plats där läsaren ska börja läsa. På så sätt kan du använda kontrollpunkter både till att markera händelser som ”klara” i underordnade program och som skydd i händelse av en redundansväxling mellan läsare som körs på olika datorer. Du kan återgå till äldre data genom att ange en lägre offset i den här kontrollpunktsprocessen. Den här mekanismen möjliggör både återhämtning vid redundansväxlingar och återuppspelning av händelseströmmar.

### <a name="common-consumer-tasks"></a>Vanliga konsumentuppgifter

Alla konsumenter i Händelsehubbar ansluter via en AMQP 1.0-session, en tillståndsmedveten dubbelriktad kommunikationskanal. Varje partition har en AMQP 1.0-session som gör det lättare att flytta händelser som åtskiljs av partitioner.

#### <a name="connect-to-a-partition"></a>Ansluta till en partition

När du ansluter till partitioner är det vanligt att använda en leasingmekanism till att samordna läsaranslutningar till specifika partitioner. På det sättet är det möjligt för varje partition i en konsumentgrupp att bara ha en aktiv läsare. Hanteringen av kontrollpunkter, leasing och läsare blir enklare med klassen [EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost) för .NET-klienter. Värden för händelsebearbetning är en smart konsumentagent.

#### <a name="read-events"></a>Läsa händelser

När en AMQP 1.0-session och -länk har öppnats för en specifik partition, levereras händelser till AMQP 1.0-klienten av händelsehubbtjänsten. Den här leveransmekanismen gör det möjligt med ett högre genomflöde och kortare svarstid än i pull-baserade mekanismer som t.ex. HTTP GET. När händelser skickas till klienten innehåller varje instans av händelsedata viktiga metadata, till exempel offset- och sekvensnumret som används för att göra det lättare att skapa kontrollpunkter i händelsesekvensen.

Händelsedata:
* Offset
* Sekvensnummer
* Innehåll
* Användaregenskaper
* Systemegenskaper

Det är ditt ansvar att hantera positionen (offset).

## <a name="capacity"></a>Kapacitet

Event Hubs har en mycket skalbar parallell arkitektur och det finns flera viktiga faktorer att tänka på när du ändrar storlek och skala.

### <a name="throughput-units"></a>Genomflödesenheter

Genomflödeskapaciteten i Event Hubs styrs av *genomflödesenheter*. Genomflödesenheter är färdiga kapacitetsenheter. En dataflödesenhet har följande kapacitet:

* Ingress (inkommande trafik): Upp till 1 MB per sekund eller 1 000 händelser per sekund (beroende på vilket som inträffar först)
* Egress (utgående trafik): Upp till 2 MB per sekund

Utöver kapaciteten för köpta genomflödesenheter är den inkommande trafiken begränsad och en [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) returneras. Utgående trafik genererar inte begränsningsundantag, men är fortfarande begränsad till kapaciteten för de köpta genomflödesenheterna. Om du får felmeddelanden om publiceringsfrekvensen eller om du förväntar dig större utgående trafik kontrollerar du hur många genomflödesenheter du har köpt för namnområdet. Du kan hantera enheter på den **skala** bladet namnområden i den [Azure-portalen](https://portal.azure.com). Du kan också hantera enheter via programmering med hjälp av [Event Hubs API: er](event-hubs-api-overview.md).

Genomflödesenheter debiteras per timme och köps i förväg. När de väl har köpts debiteras de för minst en timme. Upp till 20 genomflödesenheter kan köpas för ett Event Hubs-namnområde och delas mellan alla Event Hubs i namnområdet.

Om du vill köpa fler genomflödesenheter i block om 20 upp till 100 genomflödesenheter kontaktar du Azure-supporten. Utöver det kan du också köpa block med 100 genomflödesenheter.

Vi rekommenderar att du väga genomflödesenheter och partitioner för att uppnå bästa skala. En enstaka partition har en maximal skala på en genomflödesenhet. Antalet dataflödesenheter ska vara mindre än eller lika med antalet partitioner i en händelsehubb.

Utförlig prisinformation för Event Hubs finns i [Priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar][Event Hubs tutorial]
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Event Hubs-exempel][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Event Hubs-exempel]: https://github.com/Azure/azure-event-hubs/tree/master/samples
