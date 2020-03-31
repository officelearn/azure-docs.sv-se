---
title: Utforma program med högtillgängliga funktioner med geoupptundret lagring
titleSuffix: Azure Storage
description: Lär dig hur du använder geoupptundret lagring med läsåtkomst till arkitekten för ett program med hög tillgänglighet som är tillräckligt flexibelt för att hantera avbrott.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157100"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Designa program med hög tillgänglighet med hjälp av geo redundant lagring med läsåtkomst

En vanlig egenskap hos molnbaserade infrastrukturer som Azure Storage är att de tillhandahåller en plattform med hög tillgänglighet för program. Utvecklare av molnbaserade program måste noga överväga hur man kan utnyttja den här plattformen för att leverera program med hög tillgänglighet till sina användare. Den här artikeln fokuserar på hur utvecklare kan använda ett av Azures geo-redundanta replikeringsalternativ för att säkerställa att deras Azure Storage-program är mycket tillgängliga.

Lagringskonton som konfigurerats för geos redundant replikering replikeras synkront i den primära regionen och replikeras sedan asynkront till en sekundär region som är hundratals miles away. Azure Storage erbjuder två typer av geo-redundant replikering:

* [Geo-zon-redundant lagring (GZRS) (förhandsvisning)](storage-redundancy.md) ger replikering för scenarier som kräver både hög tillgänglighet och maximal hållbarhet. Data replikeras synkront över tre Azure-tillgänglighetszoner i den primära regionen med hjälp av zonupptäckt lagring (ZRS) och replikeras sedan asynkront till den sekundära regionen. För läsåtkomst till data i den sekundära regionen aktiverar du geo-zon redundant lagring (läsåtkomst geografisk zon redundant lagring).For read access to data in the secondary region, enable read-access geo-zone-redundant storage (RA-GZRS).
* [Geo-redundant lagring (GRS)](storage-redundancy.md) ger korsregionala replikering för att skydda mot regionala avbrott. Data replikeras synkront tre gånger i den primära regionen med lokalt redundant lagring (LRS), replikeras sedan asynkront till den sekundära regionen. För läsåtkomst till data i den sekundära regionen aktiverar du geo redundant lagring med läsåtkomst (RA-GRS).

Den här artikeln visar hur du utformar ditt program för att hantera ett avbrott i den primära regionen. Om den primära regionen blir otillgänglig kan programmet anpassas för att utföra läsåtgärder mot den sekundära regionen i stället. Kontrollera att ditt lagringskonto är konfigurerat för RA-GRS eller RA-GZRS innan du börjar.

Information om vilka primära regioner som paras ihop med vilka sekundära regioner finns i [Affärskontinuitet och haveriberedskap (BCDR): Azure Paired Regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Det finns kodavsnitt som ingår i den här artikeln och en länk till ett fullständigt exempel i slutet som du kan hämta och köra.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Ansökan design överväganden när du läser från den sekundära

Syftet med den här artikeln är att visa dig hur du utformar ett program som kommer att fortsätta att fungera (om än i en begränsad kapacitet) även i händelse av en större katastrof på det primära datacentret. Du kan utforma ditt program för att hantera tillfälliga eller långvariga problem genom att läsa från den sekundära regionen när det finns ett problem som stör läsning från den primära regionen. När den primära regionen är tillgänglig igen kan programmet återgå till läsning från den primära regionen.

Tänk på dessa viktiga punkter när du utformar ditt program för RA-GRS eller RA-GZRS:

* Azure Storage underhåller en skrivskyddad kopia av de data du lagrar i din primära region i en sekundär region. Som nämnts ovan bestämmer lagringstjänsten platsen för den sekundära regionen.

* Skrivskyddad kopia [överensstämmer så småningom](https://en.wikipedia.org/wiki/Eventual_consistency) med data i den primära regionen.

* För blobbar, tabeller och köer kan du fråga den sekundära regionen efter ett *senaste synkroniseringstidvärde* som talar om för dig när den sista replikeringen från den primära till den sekundära regionen inträffade. (Detta stöds inte för Azure-filer, som inte har RA-GRS-redundans just nu.)

* Du kan använda lagringsklientbiblioteket för att läsa och skriva data i antingen den primära eller sekundära regionen. Du kan också omdirigera läsbegäranden automatiskt till den sekundära regionen om en läsbegäran till den primära regionen time out.

* Om den primära regionen blir otillgänglig kan du initiera en växlingsöverkoppling för kontot. När du växlar över till den sekundära regionen ändras DNS-posterna som pekar på den primära regionen så att de pekar på den sekundära regionen. När redundansen är klar återställs skrivåtkomsten för GRS- och RA-GRS-konton. Mer information finns i [Redundans- och lagringskontos redundans (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md).

> [!NOTE]
> Customer-managed account failover (preview) är ännu inte tillgängligt i regioner som stöder GZRS/RA-GZRS, så kunder kan för närvarande inte hantera kontoundanställningshändelser med GZRS- och RA-GZRS-konton. Under förhandsversionen hanterar Microsoft alla redundanshändelser som påverkar GZRS/RA-GZRS-konton.

### <a name="using-eventually-consistent-data"></a>Använda så småningom konsekventa data

Den föreslagna lösningen förutsätter att det är acceptabelt att returnera potentiellt inaktuella data till det anropande programmet. Eftersom data i den sekundära regionen så småningom är konsekventa är det möjligt att den primära regionen kan bli otillgänglig innan en uppdatering till den sekundära regionen har replikerats.

Anta till exempel att kunden skickar en uppdatering, men den primära regionen misslyckas innan uppdateringen sprids till den sekundära regionen. När kunden ber att läsa tillbaka data får de inaktuella data från den sekundära regionen i stället för uppdaterade data. När du utformar ditt program måste du bestämma om detta är acceptabelt, och i så fall hur du kommer att meddela kunden. 

Senare i den här artikeln visar vi hur du kontrollerar den senaste synkroniseringstiden för sekundära data för att kontrollera om den sekundära är uppdaterad.

### <a name="handling-services-separately-or-all-together"></a>Hantering av tjänster separat eller tillsammans

Även om det är osannolikt är det möjligt för en tjänst att bli otillgänglig medan de andra tjänsterna fortfarande är fullt fungerande. Du kan hantera återförsök och skrivskyddat läge för varje tjänst separat (blobbar, köer, tabeller) eller så kan du hantera återförsök allmänt för alla lagringstjänster tillsammans.

Om du till exempel använder köer och blobbar i ditt program kan du välja att placera i separat kod för att hantera återförsöksbara fel för var och en av dessa. Om du sedan får ett nytt försök från blob-tjänsten, men kötjänsten fortfarande fungerar, påverkas bara den del av programmet som hanterar blobbar. Om du bestämmer dig för att hantera alla återförsök i lagringstjänsten allmänt och ett anrop till blob-tjänsten returnerar ett fel som kan försökas, påverkas begäranden till både blob-tjänsten och kötjänsten.

I slutändan beror detta på komplexiteten i ditt program. Du kan välja att inte hantera felen efter tjänst, utan i stället omdirigera läsbegäranden för alla lagringstjänster till den sekundära regionen och köra programmet i skrivskyddat läge när du upptäcker ett problem med någon lagringstjänst i den primära regionen.

### <a name="other-considerations"></a>Andra överväganden

Detta är de andra överväganden som vi kommer att diskutera i resten av denna artikel.

* Hantera återförsök av läsbegäranden med kretsbrytarmönstret

* Så småningom konsekventa data och den senaste synkroniseringstiden

* Testning

## <a name="running-your-application-in-read-only-mode"></a>Köra programmet i skrivskyddat läge

Om du effektivt vill förbereda ett avbrott i den primära regionen måste du kunna hantera både misslyckade läsbegäranden och misslyckade uppdateringsbegäranden (med uppdatering i det här fallet innebär infogningar, uppdateringar och borttagningar). Om den primära regionen misslyckas kan läsbegäranden omdirigeras till den sekundära regionen. Uppdateringsbegäranden kan dock inte omdirigeras till den sekundära eftersom den sekundära är skrivskyddad. Därför måste du utforma programmet så att det körs i skrivskyddat läge.

Du kan till exempel ange en flagga som är markerad innan några uppdateringsbegäranden skickas till Azure Storage. När en av uppdateringsbegärandena kommer igenom kan du hoppa över den och returnera ett lämpligt svar till kunden. Du kanske till och med vill inaktivera vissa funktioner helt och hållet tills problemet är löst och meddela användarna att dessa funktioner inte är tillgängliga för tillfället.

Om du bestämmer dig för att hantera fel för varje tjänst separat måste du också hantera möjligheten att köra ditt program i skrivskyddat läge efter tjänst. Du kan till exempel ha skrivskyddade flaggor för varje tjänst som kan aktiveras och inaktiveras. Sedan kan du hantera flaggan på lämpliga platser i koden.

Att kunna köra ditt program i skrivskyddat läge har en annan sidofördel – det ger dig möjlighet att säkerställa begränsad funktionalitet under en större programuppgradering. Du kan utlösa ditt program för att köras i skrivskyddat läge och peka på det sekundära datacentret, se till att ingen kommer åt data i den primära regionen medan du gör uppgraderingar.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Hantera uppdateringar när du kör i skrivskyddat läge

Det finns många sätt att hantera uppdateringsbegäranden när du kör skrivskyddat läge. Vi kommer inte att täcka detta utförligt, men i allmänhet finns det ett par mönster som du anser.

1. Du kan svara din användare och tala om för dem att du för närvarande inte accepterar uppdateringar. Ett kontakthanteringssystem kan till exempel göra det möjligt för kunder att komma åt kontaktinformation men inte göra uppdateringar.

2. Du kan köa dina uppdateringar i en annan region. I det här fallet skriver du väntande uppdateringsbegäranden till en kö i en annan region och sedan har ett sätt att bearbeta dessa begäranden när det primära datacentret är online igen. I det här fallet bör du meddela kunden att den begärda uppdateringen står i kö för senare bearbetning.

3. Du kan skriva dina uppdateringar till ett lagringskonto i en annan region. När det primära datacentret sedan är online igen kan du koppla uppdateringarna till de primära data som finns, beroende på datans struktur. Om du till exempel skapar separata filer med en datum-/tidsstämpel i namnet kan du kopiera tillbaka filerna till den primära regionen. Detta fungerar för vissa arbetsbelastningar som loggning och iOT-data.

## <a name="handling-retries"></a>Hantera återförsök

Azure Storage-klientbiblioteket hjälper dig att avgöra vilka fel som kan göras om. Ett 404-fel (ingen hittades resurs) skulle till exempel inte göras om eftersom det inte är troligt att det inte kommer att leda till att det lyckas igen. Å andra sidan kan ett 500-fel göras på nytt eftersom det är ett serverfel, och problemet kan helt enkelt vara ett tillfälligt problem. Mer information finns i den [öppna källkoden för klassen ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) i .NET-lagringsklientbiblioteket. (Leta efter metoden ShouldRetry.)

### <a name="read-requests"></a>Läsa begäranden

Läsbegäranden kan omdirigeras till sekundär lagring om det finns ett problem med primär lagring. Som nämnts ovan i [Använda så småningom konsekventa data](#using-eventually-consistent-data), det måste vara acceptabelt för ditt program att eventuellt läsa inaktuella data. Om du använder lagringsklientbiblioteket för att komma åt data från den sekundära kan du ange hur en läsbegäran ska försöka igen genom att ange ett värde för egenskapen **LocationMode** på något av följande:

* **PrimaryOnly** (standard)

* **PrimärTSeksekreteårskonsekt**

* **SecondaryOnly**

* **SekundärtÅrsjubileum**

När du ställer in **LocationMode** till **PrimaryThenSecondary**, om den första läsbegäran till den primära slutpunkten misslyckas med ett fel som kan göras på nytt, gör klienten automatiskt en annan läsbegäran till den sekundära slutpunkten. Om felet är en tidsgränsen för servern måste klienten vänta på att tidsgränsen upphör att gälla innan den får ett fel som kan försökas från tjänsten.

Det finns i princip två scenarier att tänka på när du bestämmer hur du ska svara på ett fel som kan försökas igen:

* Detta är ett isolerat problem och efterföljande begäranden till den primära slutpunkten returnerar inte ett fel som kan försökas igen. Ett exempel på var detta kan hända är när det finns ett tillfälligt nätverksfel.

    I det här fallet finns det ingen signifikant prestanda straff i att ha **LocationMode** inställd på **PrimaryThenSecondary** eftersom detta bara händer sällan.

* Detta är ett problem med minst en av lagringstjänsterna i den primära regionen och alla efterföljande begäranden till den tjänsten i den primära regionen kommer sannolikt att returnera återförsöksbara fel under en tidsperiod. Ett exempel på detta är om den primära regionen är helt otillgänglig.

    I det här fallet finns det en prestanda straff eftersom alla dina läsbegäranden kommer att prova den primära slutpunkten först, vänta tills timeouten upphör att gälla och växla sedan till den sekundära slutpunkten.

För dessa scenarier bör du identifiera att det finns ett pågående problem med den primära slutpunkten och skicka alla läsbegäranden direkt till den sekundära slutpunkten genom att ange egenskapen **LocationMode** till **SecondaryOnly**. För närvarande bör du också ändra programmet så att det körs i skrivskyddat läge. Den här metoden kallas [kretsbrytarmönstret](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Uppdatera begäranden

Kretsbrytarmönstret kan också tillämpas på uppdateringsbegäranden. Uppdateringsbegäranden kan dock inte omdirigeras till sekundär lagring, vilket är skrivskyddat. För dessa begäranden bör du lämna egenskapen **LocationMode** inställd på **PrimaryOnly** (standard). För att hantera dessa fel kan du använda ett mått på dessa begäranden – till exempel 10 fel i rad – och när tröskelvärdet uppfylls växlar du programmet till skrivskyddat läge. Du kan använda samma metoder för att återgå till uppdateringsläge som de som beskrivs nedan i nästa avsnitt om kretsbrytarmönstret.

## <a name="circuit-breaker-pattern"></a>Strömbrytarmönstret

Om du använder kretsbrytarmönstret i programmet kan det hindras från att försöka igen en åtgärd som sannolikt kommer att misslyckas upprepade gånger. Det gör att programmet kan fortsätta att köras i stället för att ta upp tid medan åtgärden är retried exponentiellt. Det upptäcker också när felet har åtgärdats, då programmet kan försöka åtgärden igen.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Så här implementerar du kretsbrytarmönstret

Om du vill identifiera att det finns ett pågående problem med en primär slutpunkt kan du övervaka hur ofta klienten stöter på fel som kan försökas igen. Eftersom varje fall är olika måste du bestämma vilket tröskelvärde du vill använda för beslutet att växla till den sekundära slutpunkten och köra programmet i skrivskyddat läge. Du kan till exempel välja att utföra växeln om det finns 10 fel i rad utan lyckade resultat. Ett annat exempel är att växla om 90 % av begärandena under en tvåminutersperiod misslyckas.

I det första scenariot kan du helt enkelt hålla antalet fel, och om det blir en framgång innan du når det maximala, ställ in antalet till noll. För det andra scenariot är ett sätt att implementera det att använda MemoryCache-objektet (i .NET). För varje begäran lägger du till en CacheItem i cacheminnet, anger värdet till lyckad (1) eller misslyckas (0) och ställer in förfallotiden till 2 minuter från och med nu (eller vad din tidsbegränsning är). När en transaktions förfallotid har uppnåtts tas posten automatiskt bort. Detta ger dig en rullande 2-minuters fönster. Varje gång du gör en begäran till lagringstjänsten använder du först en Linq-fråga över MemoryCache-objektet för att beräkna procentframgången genom att summera värdena och dividera med antalet. När procentframgången sjunker under ett tröskelvärde (till exempel 10 %) anger du egenskapen **LocationMode** för läsbegäranden till **SecondaryOnly** och växlar programmet till skrivskyddat läge innan du fortsätter.

Tröskelvärdet för fel som används för att avgöra när bytet ska ändras kan variera från service till service i ditt program, så du bör överväga att göra dem konfigurerbara parametrar. Det är också här du bestämmer dig för att hantera återförsöksbara fel från varje tjänst separat eller som en, som diskuterats tidigare.

En annan faktor är hur du hanterar flera instanser av ett program och vad du ska göra när du upptäcker återförsöksbara fel i varje instans. Du kan till exempel ha 20 virtuella datorer som körs med samma program inläst. Hanterar du varje instans separat? Om en instans börjar få problem, vill du begränsa svaret till just den instansen, eller vill du försöka få alla instanser att svara på samma sätt när en instans har ett problem? Det är mycket enklare att hantera instanserna separat än att försöka samordna svaret mellan dem, men hur du gör detta beror på programmets arkitektur.

### <a name="options-for-monitoring-the-error-frequency"></a>Alternativ för övervakning av felfrekvensen

Du har tre huvudalternativ för att övervaka frekvensen för återförsök i den primära regionen för att avgöra när du ska växla över till den sekundära regionen och ändra programmet så att det körs i skrivskyddat läge.

* Lägg till en hanterare för händelsen [**Försöker igen**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) på [**det OperationContext-objekt**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) som du skickar till dina lagringsbegäranden – det här är den metod som visas i den här artikeln och som används i det medföljande exemplet. Dessa händelser aktiveras när klienten försöker en begäran igen, så att du kan spåra hur ofta klienten stöter på fel som kan försökas på en primär slutpunkt.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* I metoden [**Utvärdera**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) i en anpassad återförsöksprincip kan du köra anpassad kod när ett nytt försök sker. Förutom att spela in när ett nytt försök inträffar, ger detta dig också möjlighet att ändra ditt återförsök beteende.

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

* Den tredje metoden är att implementera en anpassad övervakningskomponent i ditt program som kontinuerligt pingar din primära lagringsslutpunkt med dummy läsbegäranden (till exempel läsa en liten blob) för att avgöra dess hälsa. Detta skulle ta upp vissa resurser, men inte en betydande mängd. När ett problem upptäcks som når tröskelvärdet utför du sedan växeln till **SecondaryOnly-** och skrivskyddat läge.

Vid något tillfälle vill du växla tillbaka till att använda den primära slutpunkten och tillåta uppdateringar. Om du använder en av de två första metoderna ovan kan du helt enkelt växla tillbaka till den primära slutpunkten och aktivera uppdateringsläge efter att en godtyckligt vald tid eller ett antal åtgärder har utförts. Du kan sedan låta den gå igenom logiken för återförsök igen. Om problemet har åtgärdats fortsätter den att använda den primära slutpunkten och tillåta uppdateringar. Om det fortfarande finns ett problem växlar det återigen tillbaka till den sekundära slutpunkten och skrivskyddat läge efter att de kriterier du har angett har misslyckats.

I det tredje scenariot kan du utlösa växeln igen till **PrimaryOnly** när pingning av den primära lagringsslutpunkten blir lyckad igen och fortsätta tillåta uppdateringar.

## <a name="handling-eventually-consistent-data"></a>Hantera så småningom konsekventa data

Geo-redundant lagring fungerar genom att replikera transaktioner från den primära till den sekundära regionen. Den här replikeringsprocessen garanterar att data i den sekundära regionen *så småningom*är konsekventa . Detta innebär att alla transaktioner i den primära regionen så småningom kommer att visas i den sekundära regionen, men att det kan finnas en fördröjning innan de visas, och att det inte finns någon garanti för att transaktionerna kommer till den sekundära regionen i samma ordning som den där de tillämpades ursprungligen i den primära regionen. Om dina transaktioner kommer i den sekundära regionen i fel ordning *kan* du anse att dina data i den sekundära regionen är i ett inkonsekvent tillstånd tills tjänsten kommer ikapp.

I följande tabell visas ett exempel på vad som kan hända när du uppdaterar information om en medarbetare så att de är medlemmar i *administratörsrollen.* Av hänsyn till det här exemplet kräver detta att du uppdaterar **medarbetarentiteten** och uppdaterar en **administratörsrollentitet** med ett antal av det totala antalet administratörer. Lägg märke till hur uppdateringarna tillämpas i oordning i den sekundära regionen.

| **Tid** | **Transaktion**                                            | **Replikering**                       | **Senaste synkroniseringstid** | **Resultat** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transaktion A: <br> Infoga medarbetare <br> enhet i primär |                                   |                    | Transaktion A infogad i primär,<br> inte replikeras ännu. |
| T1       |                                                            | Transaktion A <br> replikeras till<br> Sekundära | T1 | Transaktion A replikeras till sekundär. <br>Senaste synkroniseringstid uppdaterad.    |
| T2       | Transaktion B:<br>Uppdatering<br> medarbetarens enhet<br> i primär  |                                | T1                 | Transaktion B skriven till primär,<br> inte replikeras ännu.  |
| T3       | Transaktion C:<br> Uppdatering <br>administratör<br>rollenhet i<br>Primära |                    | T1                 | Transaktion C skriven till primär,<br> inte replikeras ännu.  |
| *T4*     |                                                       | Transaktion C <br>replikeras till<br> Sekundära | T1         | Transaktion C replikeras till sekundär.<br>LastSyncTime har inte uppdaterats eftersom <br>transaktion B har inte replikerats ännu.|
| *T5 (på andra)*     | Läsa entiteter <br>från sekundär                           |                                  | T1                 | Du får det inaktuella värdet för medarbetaren <br> enhet eftersom transaktion B inte har <br> replikerat ännu. Du får det nya värdet för<br> administratörsrollentiteten eftersom C har<br> Replikerade. Senaste synkroniseringstiden har fortfarande inte<br> uppdaterats eftersom transaktion B<br> har inte replikerat. Du kan berätta för<br>administratörsrollentiteten är inkonsekvent <br>eftersom entitetens datum/tid är ute efter <br>Senaste synkroniseringstiden. |
| *T6 (på andra) sätt*     |                                                      | Transaktion B<br> replikeras till<br> Sekundära | T6 (på andra) sätt                 | *T6* – Alla transaktioner via C har <br>har replikerats, Senaste synkroniseringstid<br> uppdateras. |

I det här exemplet antar du att klienten växlar till läsning från den sekundära regionen vid T5. Den kan läsa **administratörsrollentiteten** just nu, men entiteten innehåller ett värde för antalet administratörer som inte överensstämmer med antalet **medarbetarentiteter** som är markerade som administratörer i den sekundära regionen just nu. Din klient kan helt enkelt visa detta värde, med risk för att det är inkonsekvent information. Alternativt kan klienten försöka fastställa att **administratörsrollen** är i ett potentiellt inkonsekvent tillstånd eftersom uppdateringarna har skett i oordning och sedan informera användaren om detta faktum.

Om du vill känna igen att den har potentiellt inkonsekventa data kan klienten använda värdet för den *senaste synkroniseringstiden* som du kan få när som helst genom att fråga en lagringstjänst. Detta talar om för dig den tid då data i den sekundära regionen senast var konsekventa och när tjänsten hade tillämpat alla transaktioner före den tidpunkten. I exemplet ovan, när tjänsten har **employee** infogat medarbetarentiteten i den sekundära regionen, är den senaste synkroniseringstiden inställd på *T1*. Den finns kvar på *T1* tills tjänsten uppdaterar **medarbetareniteten** i den sekundära regionen när den är inställd på *T6*. Om klienten hämtar den senaste synkroniseringstiden när den läser entiteten på *T5*kan den jämföra den med tidsstämpeln på entiteten. Om tidsstämpeln på entiteten är senare än den senaste synkroniseringstiden är entiteten i ett potentiellt inkonsekvent tillstånd och du kan vidta vad som är lämplig åtgärd för ditt program. Om du använder det här fältet måste du veta när den senaste uppdateringen av den primära slutfördes.

Mer information om hur du kontrollerar den senaste synkroniseringstiden finns [i Kontrollera egenskapen Senaste synkroniseringstid för ett lagringskonto](last-sync-time-get.md).

## <a name="testing"></a>Testning

Det är viktigt att testa att ditt program fungerar som förväntat när det stöter på fel som kan försökas igen. Du måste till exempel testa att programmet växlar till sekundärt och skrivskyddat läge när det upptäcker ett problem och växlar tillbaka när den primära regionen blir tillgänglig igen. För att göra detta behöver du ett sätt att simulera fel som kan försökas på nytt och styra hur ofta de inträffar.

Du kan använda [Fiddler](https://www.telerik.com/fiddler) för att fånga upp och ändra HTTP-svar i ett skript. Det här skriptet kan identifiera svar som kommer från din primära slutpunkt och ändra HTTP-statuskoden till en som storage client-biblioteket känner igen som ett fel som kan försökas på nytt. Det här kodavsnittet visar ett enkelt exempel på ett Fiddler-skript som fångar upp svar på läsbegäranden mot tabellen **med employeedata** för att returnera en 502-status:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Du kan utöka det här exemplet till att fånga upp ett bredare antal begäranden och bara ändra **responseCode** på vissa av dem för att bättre simulera ett verkligt scenario. Mer information om hur du anpassar Fiddler-skript finns i [Ändra en begäran eller ett svar](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) i Fiddler-dokumentationen.

Om du har gjort tröskelvärdena för att växla ditt program till skrivskyddat läge konfigurerbart blir det enklare att testa beteendet med icke-produktionstransaktionsvolymer.

## <a name="next-steps"></a>Efterföljande moment

* Mer information om hur du läser från den sekundära regionen, inklusive ett annat exempel på hur egenskapen Senaste synkroniseringstid har angetts, finns i [Azure Storage Redundansalternativ och Läs åtkomst Geo-Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Ett fullständigt exempel som visar hur du gör växeln fram och tillbaka mellan de primära och sekundära slutpunkterna finns i [Azure-exempel – Använda kretsbrytarmönstret med RA-GRS-lagring](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
