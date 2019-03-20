---
title: Utforma högtillgängliga Aaplications med hjälp av read-access geo-redundant lagring (RA-GRS) | Microsoft Docs
description: Hur du använder Azure RA-GRS-lagring för att skapa ett program som är tillräckligt flexibelt för att hantera avbrott och som har med hög tillgänglighet.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/17/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: be1c46c5bc2c8edcfeca81c82095687c4ddfd894
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225832"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Utforma högtillgängliga program med hjälp av RA-GRS

En vanlig funktion för molnbaserade infrastrukturer som Azure Storage är att de tillhandahåller en högtillgänglig plattform för hantering av. Utvecklare av molnbaserade program måste noggrant igenom hur du kan använda den här plattformen för att leverera högtillgängliga program till sina användare. Den här artikeln handlar om hur utvecklare kan använda Geo-Redundant lagring med läsbehörighet (RA-GRS) så som sina Azure Storage-program med hög tillgänglighet.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Den här artikeln fokuserar på GRS och RA-GRS. Med GRS hålls tre kopior av dina data i den primära regionen som du valde när du konfigurerar storage-konto. Tre ytterligare kopior bevaras asynkront i en sekundär region som anges av Azure. RA-GRS erbjuder geo-redundant lagring med läsbehörighet till den sekundära kopian.

Information om vilka primära regioner är länkat till vilka sekundära regioner finns i [företag affärskontinuitet och haveriberedskap recovery (BCDR): Parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Det finns kodavsnitt som ingår i den här artikeln och en länk till ett fullständigt exempel i slutet som du kan ladda ned och köra.

> [!NOTE]
> Azure Storage stöder nu zonredundant lagring (ZRS) för att skapa program med hög tillgänglighet. ZRS ger en enkel lösning för många program redundans behov. ZRS ger skydd mot maskinvarufel eller oåterkalleligt katastrofer som påverkar ett enda datacenter. Mer information finns i [zonen-redundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Viktiga funktioner i RA-GRS

Tänk på dessa nyckelpunkter när du utformar ditt program för RA-GRS:

* Azure Storage har en skrivskyddad kopia av de data du lagrar i din primära region i en sekundär region. Som nämnts ovan anger lagringstjänsten platsen för den sekundära regionen.

* Skrivskyddad kopia är [konsekvent](https://en.wikipedia.org/wiki/Eventual_consistency) med data i den primära regionen.

* För blobbar, tabeller och köer, kan du fråga den sekundära regionen för en *senaste synkroniseringstid* värde som anger när den senaste replikeringen från primärt till den sekundära regionen inträffade. (Detta stöds inte för Azure Files, som inte har RA-GRS redundans just nu.)

* Du kan använda Storage-klientbiblioteket för att interagera med data i den primära eller sekundära regionen. Du kan också omdirigera diskläsningsbegäranden automatiskt till den sekundära regionen om tidsgränsen uppnås för en läsbegäran till den primära regionen.

* Om den primära regionen blir otillgänglig, kan du initiera en konto-redundans. När du växlar över till den sekundära regionen har DNS-poster som pekar på den primära regionen ändrats för att peka på den sekundära regionen. När redundansväxlingen är klar återställs skrivbehörighet för GRS och RA-GRS-konton. Mer information finns i [Disaster recovery och storage-konto redundans (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md).

## <a name="application-design-considerations-when-using-ra-grs"></a>Designöverväganden när du använder RA-GRS

Syftet med den här artikeln är att visa dig hur du utformar ett program som fortsätter att fungera (i en begränsad kapacitet) även i händelse av en större katastrof på det primära datacentralen. Du kan utforma ditt program för att hantera tillfälliga eller långvariga problem genom att läsa från den sekundära regionen när det finns ett problem som stör läsning från den primära regionen. När den primära regionen är tillgänglig igen, kan programmet tillbaka till läsning från den primära regionen.

### <a name="using-eventually-consistent-data"></a>Med hjälp av konsekvent data

Föreslagen lösning förutsätter att det går att returnera potentiellt inaktuella data till det anropande programmet. Eftersom data i den sekundära regionen är konsekvent, är det möjligt den primära regionen blir otillgänglig innan en uppdatering till den sekundära regionen är klar replikeras.

Anta exempelvis att kunden skickar en uppdatering har, men den primära regionen misslyckas innan uppdateringen sprids till den sekundära regionen. När kunden begär att läsa data tillbaka, får han inaktuella data från den sekundära regionen i stället för uppdaterade data. När ditt program måste du bestämma om det är lämpligt och i så fall, hur du kommer meddelandet kunden. 

Senare i den här artikeln visar vi hur du kontrollerar den senaste synkronisering för de sekundära data för att kontrollera om sekundärt är uppdaterad.

### <a name="handling-services-separately-or-all-together"></a>Hantera tjänster separat eller allt på samma plats

Men det är osannolikt, är det möjligt för en tjänst blir otillgänglig medan de andra tjänsterna är fortfarande helt funktionella. Du kan hantera återförsök och skrivskyddat läge för varje tjänst separat (blobs, köer, tabeller), eller du kan hantera återförsök med det allmänna skyddet för alla lagringstjänster tillsammans.

Om du använder köer och blobbar i ditt program, kanske du exempelvis vill placera i separat kod för att hantera återförsökbart fel för var och en av dessa. Sedan om du får ett nytt försök från blob service och kötjänsten fungerar fortfarande, kommer endast en del av ditt program som hanterar blobar att påverkas. Om du vill hantera alla storage service återförsök med allmänt skydd och ett anrop till blobtjänsten returnerar ett återförsökbart fel, påverkas förfrågningar till både blob service och kötjänsten.

Slutligen beror detta på komplexiteten i ditt program. Du behöver inte hantera felen av tjänsten, men i stället för att omdirigera begäranden för alla lagringstjänster som till den sekundära regionen och köra programmet i skrivskyddat läge när du upptäcker ett problem med vilken lagringstjänst som i den primära regionen.

### <a name="other-considerations"></a>Annat att tänka på

Det här är de andra överväganden som diskuteras i resten av den här artikeln.

*   Hanterar återförsök för läsbegäranden med hjälp av kretsbrytarmönstret

*   Eventuell konsekvens data och tid för senaste synkronisering

*   Testning

## <a name="running-your-application-in-read-only-mode"></a>Kör ditt program i skrivskyddat läge

Om du vill använda RA-GRS-lagring måste kunna hantera båda misslyckade läsbegäranden och misslyckade uppdateringsbegäranden (med uppdateringen i det här fallet innebär infogningar, uppdateringar och borttagningar). Om de primära datacentret misslyckas, kan du läsa begäranden kan omdirigeras till det sekundära datacentralen. Begäranden om att uppdatera går inte att omdirigeras till sekundärt eftersom sekundärt är skrivskyddad. Därför måste du utforma programmet att köras i skrivskyddat läge.

Du kan till exempel ange en flagga som kontrolleras innan alla begäranden om att uppdatera skickas till Azure Storage. När en uppdatering begäran kommer kan du hoppa över den och returnera ett lämpligt svar till kunden. Du kanske även vill inaktivera vissa funktioner funktionen helt och hållet förrän problemet har åtgärdats och meddela användarna att dessa funktioner är inte tillgänglig för tillfället.

Om du vill hantera fel för varje tjänst separat, måste du också hantera möjligheten att köra ditt program i skrivskyddat läge av tjänsten. Du kan till exempel ha skrivskyddad flaggor för varje tjänst som kan aktiveras och inaktiveras. Du kan sedan hantera flaggan i lämpliga platser i din kod.

Att kunna köra ditt program i skrivskyddat läge har en annan sida fördel – den ger dig möjlighet att se till att begränsade funktioner under en stor uppgradering. Du kan utlösa ditt program att köra i skrivskyddat läge och pekar på det sekundära datacentret att se till att ingen har åtkomst till data i den primära regionen medan du gör uppgraderingar.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Hantering av uppdateringar när de körs i skrivskyddat läge

Det finns många sätt att hantera begäranden om att uppdatera när du kör i skrivskyddat läge. Vi kommer inte upp detta ett mer omfattande sätt, men det finns ofta ett par mönster som du vill.

1.  Du kan svara på användaren och be dem om du inte just nu tar emot uppdateringar. Ett system för hantering av kan till exempel möjligt för kunderna att komma åt kontaktinformation men inte göra några uppdateringar.

2.  Du kan placera dina uppdateringar i en annan region. I det här fallet skulle du skrivförfrågningar väntande uppdatering till en kö i en annan region och har ett sätt att bearbeta dessa när det primära datacentralen är online igen. I det här scenariot, bör du låta kunden vet att uppdateringen begärt står i kö för senare bearbetning.

3.  Du kan skriva dina uppdateringar till ett lagringskonto i en annan region. När det primära datacentralen är online igen kan du låta ett sätt att slå samman dessa uppdateringar till den primära data, beroende på strukturen för data. Om du skapar separata filer med en datum-/ tidsstämpel i namnet kan kopiera du filerna till den primära regionen. Detta fungerar för vissa arbetsbelastningar, till exempel loggnings- och iOT-data.

## <a name="handling-retries"></a>Hanterar återförsök

Hur vill du veta vilka fel är återförsökbart? Detta bestäms av storage-klientbiblioteket. Ett 404-fel (resursen hittades inte) är exempelvis inte återförsökbart eftersom försöker den inte är sannolikt att lyckas. Å andra sidan, är ett 500-fel återförsökbart eftersom det är ett serverfel och det kan vara ett övergående problem. Mer information finns i [öppna källkoden för klassen ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) i storage-klientbiblioteket för .NET. (Leta efter metoden ShouldRetry).

### <a name="read-requests"></a>Läsförfrågningar

Läsbegäranden kan omdirigeras till sekundär lagring om det finns ett problem med primär lagring. Som beskrivs ovan i [med så småningom konsekvent Data](#using-eventually-consistent-data), det måste vara godkänd för programmet att läsa potentiellt inaktuella data. Om du använder storage-klientbiblioteket åtkomst till RA-GRS-data, kan du ange återförsöksbeteendet av en läsbegäran genom att ange ett värde för den **LocationMode** egenskap enligt en av följande:

*   **PrimaryOnly** (standard)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

När du ställer in den **LocationMode** till **PrimaryThenSecondary**, om den första skrivskyddade begäran till den primära slutpunkt fungerar inte med ett återförsökbart fel klienten gör automatiskt en annan läsbegäran till den sekundär slutpunkt. Om felet är en tidsgräns för server, har klienten att vänta tills tidsgränsen att gälla innan den får ett återförsökbart fel från tjänsten.

Det finns två scenarier för att tänka på när du bestämmer hur du svarar på ett återförsökbart fel i princip:

*   Detta är det problem med isolerade och efterföljande förfrågningar till den primära slutpunkten returnerar inte ett återförsökbart fel. Ett exempel på där detta kan inträffa är när det finns ett tillfälligt nätverksfel.

    I det här scenariot, det finns inga betydande prestandaproblem i med **LocationMode** inställd **PrimaryThenSecondary** som det här inträffar bara sällan.

*   Detta är ett problem med minst en av lagringstjänsterna i den primära regionen och alla efterföljande förfrågningar till tjänsten i den primära regionen kommer sannolikt att returnera återförsökbart fel för en viss tidsperiod. Ett exempel på detta är om den primära regionen är helt otillgänglig.

    I det här scenariot finns det en prestandaförsämring eftersom alla läsbegäranden kommer försöka den primära slutpunkten först, vänta tills tidsgränsen på att gå ut och sedan växla till den sekundära slutpunkten.

I dessa scenarier bör du identifiera som det finns en pågående problem med den primära slutpunkten och skicka läser alla begäranden direkt till den sekundära slutpunkten genom att ange den **LocationMode** egenskap **SecondaryOnly** . För tillfället bör du också ändra programmet att köras i skrivskyddat läge. Den här metoden kallas den [Kretsbrytarmönstret](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Begäranden om att uppdatera

Kretsbrytarmönstret kan också användas för att uppdatera begäranden. Begäranden om att uppdatera går inte att omdirigeras till sekundär lagring som är skrivskyddad. För sådana begäranden, bör du lämna den **LocationMode** egenskapen **PrimaryOnly** (standard). För att hantera de här felen du gäller ett mått för dessa begäranden – till exempel 10 fel i en rad – och när tröskeln för ditt är uppfyllt, växlar programmet i skrivskyddat läge. Du kan använda samma metoder för att returnera för att uppdatera läge som de som beskrivs nedan i nästa avsnitt om kretsbrytarmönstret.

## <a name="circuit-breaker-pattern"></a>Strömbrytarmönstret

Med hjälp av kretsbrytarmönstret i ditt program kan förhindra att den återförsök för en åtgärd som troligen kommer att misslyckas upprepade gånger. Det kan programmet fortsätta att köras i stället för att tar upp tid medan åtgärden görs ett nytt exponentiellt. Dessutom upptäcks när felet har åtgärdats, då kan programmet försöka igen.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Hur du implementerar kretsbrytarmönstret

Du kan se att det finns ett aktuellt problem med en primär slutpunkt kan du övervaka hur ofta klienten påträffar återförsökbart fel. Eftersom varje fall är olika, måste du fatta beslut om tröskelvärdet som du vill använda för beslutet att växla till den sekundära slutpunkten och kör programmet i skrivskyddat läge. Du kan till exempel välja att genomföra växeln om det finns 10 fel i en rad med ingen lyckade. Ett annat exempel är att byta om 90% av begäranden under en 2-minutersperiod misslyckas.

För det första scenariot kan du helt enkelt hålla en uppräkning av felen och om det finns en succé innan de når maximum, anger du antalet till noll. För det andra scenariot är ett sätt att implementera den att använda objektet minnescache (i .NET). För varje begäran, lägga till en CacheItem i cacheminnet, Ställ in värdet på klar (1) eller misslyckas (0) och ange förfallotiden till 2 minuter från nu (eller vad den tid begränsningen är). När en post förfallotid har uppnåtts kan tas posten bort automatiskt. Detta ger dig ett rullande fönster 2 minuter. Varje gång du gör en begäran till storage-tjänsten använda du först en Linq-fråga över objektet minnescache för att beräkna procent lyckade genom att summera värdena och dividera med antalet. När procent lyckade sjunker under vissa tröskelvärde (till exempel 10%), ange den **LocationMode** egenskapen för Läs förfrågningar om att **SecondaryOnly** och växla program i skrivskyddat läge innan du fortsätter.

Tröskelvärde för fel som används för att avgöra när du ska göra växeln kan variera från tjänster i ditt program bör du tänka på att göra dem konfigurerbara parametrar. Det är också där du vill hantera återförsökbart fel från varje tjänst separat eller som en, vilket beskrivs ovan.

Ett annat övervägande är hur du hanterar flera instanser av ett program, och vad du ska göra när du upptäcker återförsökbart fel i varje instans. Du kan till exempel har 20 virtuella datorer som körs med samma program läses in. Ska du hantera varje instans separat? Om en instans startar problem, vill du begränsar svaret till precis som en instans eller vill du försöka att svara alla instanser på samma sätt som när en instans har ett problem? Hantering av instanserna separat är mycket enklare än att försöka att koordinera svaret mellan dem, men hur du gör detta beror på ditt programs arkitektur.

### <a name="options-for-monitoring-the-error-frequency"></a>Alternativ för att övervaka frekvensen för fel

Har du tre huvudsakliga alternativ för att övervaka frekvensen för återförsök i den primära regionen för att avgöra när du växlar över till den sekundära regionen och ändra programmet att köras i skrivskyddat läge.

*   Lägger till en hanterare för den [ **försöker igen** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) händelse på den [ **OperationContext** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) objekt som du skickar till lagringen begär – det här är metoden visas i den här artikeln och används i den medföljande exemplet. Dessa händelser utlöses varje gång klienten försöker en begäran, så att du kan spåra hur ofta klienten påträffar återförsökbart fel på en primär slutpunkt.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   I den [ **Evaluate** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) -metod i en egen återförsöksprincip, du kan köra anpassad kod varje gång ett nytt försök utförs. Förutom att registrera när ett nytt försök sker, detta ger dig också en möjlighet att ändra beteende för återförsök.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

*   Den tredje metoden är att implementera en anpassad övervakningskomponent i programmet som pingar kontinuerligt din primära lagringsslutpunkt med dummy diskläsningsbegäranden (t.ex läsningen av en liten blob) till dess hälsa. Detta kan ta några resurser, men inte mycket. När ett problem har identifierats som når tröskeln för ditt kan du sedan utför övergången till **SecondaryOnly** och skrivskyddat läge.

Vid en viss tidpunkt kommer du vill växla tillbaka till med hjälp av den primära slutpunkten så att uppdateringar. Om du använder en av de två första metoderna ovan kan du helt enkelt gå tillbaka till den primära slutpunkten och aktivera uppdateringsläget när ett godtyckligt valda tid eller antalet åtgärder har utförts. Sedan kan du låta den går igenom omprövningslogiken igen. Om problemet har åtgärdats, fortsätter den att använda den primära slutpunkten och tillåter uppdateringar. Om det finns fortfarande ett problem, kommer det en gång till växla tillbaka till den sekundära slutpunkten och skrivskyddat läge efter de kriterier som du har ställt in.

I det tredje scenariot när pinga den primära lagringsslutpunkten blir lyckad igen, du kan utlösa växeln tillbaka till **PrimaryOnly** och fortsätta att tillåta uppdateringar.

## <a name="handling-eventually-consistent-data"></a>Hantering av konsekvent data

Med RA-GRS replikeras transaktioner från den primära regionen till den sekundära. Replikeringsprocessen garanterar att data i den sekundära regionen är *konsekvent*. Det innebär att alla transaktioner i den primära regionen så småningom kommer att visas i den andra regionen, men det kan finnas en fördröjning innan de kan visas och att det finns ingen garanti transaktioner som tas emot i den sekundära regionen i samma ordning som där de tillämpades ursprungligen i den primära regionen. Om dina transaktioner kommer till den sekundära regionen fel ordning, du *kan* överväga dina data i den sekundära regionen ska vara i ett inkonsekvent tillstånd förrän tjänsten ikapp.

I följande tabell visar ett exempel på vad som händer när du uppdaterar information om en anställd att göra henne medlem i den *administratörer* roll. För det här exemplet är detta kräver att du uppdaterar den **medarbetare** entitet och uppdatera en **administratörsroll** entitet med en uppräkning av det totala antalet administratörer. Observera hur uppdateringarna tillämpas inte i den sekundära regionen.

| **Time** | **Transaktionen**                                            | **Replikering**                       | **Senaste synkroniseringstid** | **Resultatet** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transaktionen A: <br> Infoga medarbetare <br> entiteten i primär |                                   |                    | Transaktionen A infogas till primär,<br> inte har replikerats än. |
| T1       |                                                            | Transaktionen A <br> replikeras till<br> Sekundär | T1 | Transaktionen A replikeras till sekundär. <br>Senaste synkronisering har uppdaterats.    |
| T2       | Transaktionen B:<br>Uppdatering<br> Medarbetaren entitet<br> i primär  |                                | T1                 | Transaktionen B som skrivs till primär,<br> inte har replikerats än.  |
| T3       | Transaktionen C:<br> Uppdatering <br>administratör<br>rollen entitet i<br>primär |                    | T1                 | Transaktionen skrivs till primär, C<br> inte har replikerats än.  |
| *T4*     |                                                       | Transaktionen C <br>replikeras till<br> Sekundär | T1         | Transaktionen C som replikeras till sekundär.<br>LastSyncTime inte uppdateras eftersom <br>transaktionen B har ännu inte replikerats.|
| *T5*     | Läsa entiteter <br>från den sekundära                           |                                  | T1                 | Du får det inaktuella värdet för medarbetare <br> entiteten eftersom transaktionen B har inte <br> replikerade ännu. Du får det nya värdet för<br> administratören rollentiteten eftersom C har<br> replikeras. Senaste synkroniseringstid fortfarande inte<br> har uppdaterats eftersom transaktionen B<br> har inte replikeras. Du kan se den<br>administratören rollentiteten är inkonsekvent <br>eftersom entiteten datum/tid är efter <br>Senaste synkronisering. |
| *T6*     |                                                      | Transaktionen B<br> replikeras till<br> Sekundär | T6                 | *T6* – alla transaktioner via C <br>har replikerats, senaste synkroniseringstid<br> har uppdaterats. |

I det här exemplet antar vi att klienten växlar till läsning från den sekundära regionen på T5. Det kan läsa den **administratörsroll** entiteten just nu, men entitet som innehåller ett värde för antalet administratörer som inte stämmer överens med antalet **medarbetare** entiteter som är markerad som administratörer i den sekundära regionen just nu. Klienten kan bara visa det här värdet med risk att den är inkonsekvent information. Du kan också klienten kan försöka fastställa som den **administratörsroll** är i ett eventuellt inkonsekvent tillstånd eftersom uppdateringarna vara har fel ordning och informera användaren om detta.

Att identifiera att den har potentiellt inkonsekventa data kan klienten använda värdet för den *senaste synkroniseringstid* att du kan få när som helst genom att fråga en lagringstjänst. Anger det tiden när data i den sekundära regionen senast konsekvent och när tjänsten har tillämpat alla transaktioner före den punkten i tiden. I exemplet som visas ovan, när tjänsten infogar den **medarbetare** entitet i den sekundära regionen, den senaste synkronisering är inställd på *T1*. Den ligger kvar på *T1* tills tjänstuppdateringar den **medarbetare** entitet i den sekundära regionen när den är inställd på *T6*. Om klienten hämtar den senaste synkronisering när det läser entiteten vid *T5*, det kan jämföra den med tidsstämpeln på entiteten. Om tidsstämpeln på entiteten är senare än den senaste synkronisering, sedan entiteten är i ett eventuellt inkonsekvent tillstånd du kan vidta för det som är lämplig åtgärd för ditt program. Använda det här fältet krävs att du vet när den senaste uppdateringen av primärt slutfördes.

## <a name="testing"></a>Testning

Det är viktigt att testa att ditt program fungerar som förväntat när återförsökbart fel påträffas. Till exempel behöver du testa att programmet växlar till sekundärt och i skrivskyddat läge när det upptäcker ett problem och växlar tillbaka när den primära regionen blir tillgänglig igen. Om du vill göra detta måste behöver du ett sätt att simulera återförsökbart fel och styra hur ofta de inträffar.

Du kan använda [Fiddler](https://www.telerik.com/fiddler) att komma åt och ändra HTTP-svar i ett skript. Det här skriptet kan identifiera svar som kommer från din primära slutpunkt och ändra HTTP-statuskoden till en Storage-klientbiblioteket känner igen som ett återförsökbart fel. Det här kodstycket visar ett enkelt exempel på ett Fiddler-skript som fångar upp svar om du vill läsa förfrågningar mot den **employeedata** tabell för att returnera statusen 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Du kan utöka det här exemplet för att fånga upp ett bredare spektrum av begäranden och bara ändra den **responseCode** på några av dem att bättre simulera ett verkligt scenario. Mer information om hur du anpassar Fiddler skript finns i [ändra en begäran eller ett svar](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) i Fiddler-dokumentationen.

Om du har gjort tröskelvärdena för att växla ditt program till skrivskyddat läge kan konfigureras, är det lättare att testa beteende med icke-produktion transaktionsvolymer.

## <a name="next-steps"></a>Nästa steg

* Mer information om Read Access Geo-redundans, inklusive ett annat exempel på hur LastSyncTime ställs finns [redundansalternativ för Windows Azure Storage och Geo-Redundant lagring med läsbehörighet](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Ett komplett exempel som visar hur du att växla fram och tillbaka mellan primära och sekundära slutpunkter finns i [Azure-exempel – med Kretsbrytarmönstret med RA-GRS-lagring](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
