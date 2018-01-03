---
title: "Designa hög tillgängliga program med hjälp av Azure Geo-Redundant lagring med läsbehörighet (RA-GRS) | Microsoft Docs"
description: "Hur du använder Azure RA-GRS lagring och skapa en högtillgänglig program som är tillräckligt flexibelt för att hantera avbrott."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/11/2017
ms.author: tamram
ms.openlocfilehash: fe7c6d1f2530b43ac7b10c5b6b0723452452a97a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Designa hög tillgängliga program med hjälp av RA-GRS

En vanlig funktion för molnbaserad infrastruktur som Azure Storage är de ger en plattform för hög tillgänglighet som värd för program. Utvecklare av molnbaserade program måste noggrant igenom hur man utnyttjar den här plattformen för att tillhandahålla hög tillgänglighet program till sina användare. Den här artikeln fokuserar på hur utvecklare kan använda Geo-Redundant lagring med läsbehörighet (RA-GRS) så som sina Azure Storage-program är hög tillgänglighet.

Azure Storage erbjuder fyra alternativ för redundans för data i ditt lagringskonto:

- LRS (lokalt Redundant lagring)
- ZRS (zonen Redundant lagring) 
- GRS (Geo-Redundant lagring)
- RA-GRS (Geo-Redundant lagring med läsbehörighet). 

Den här artikeln fokuserar på GRS och RA-GRS. Med GRS hålls tre kopior av dina data i den primära region som du valde när du ställer in storage-konto. Tre ytterligare kopior underhålls asynkront i en sekundär region som anges av Azure. RA-GRS är detsamma som GRS förutom att du har läsbehörighet till den alternativa kopian. Mer information om de olika alternativen för Azure Storage-redundans finns [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/storage-redundancy). Replikering artikeln visar också pairings av de primära och sekundära regionerna.

Det finns kodstycken som ingår i den här artikeln och en länk till ett fullständigt exempel slutet som du kan hämta och köra.

## <a name="key-features-of-ra-grs"></a>Viktiga funktioner i RA-GRS

Tänk på dessa viktiga punkter när du skapar ditt program för RA-GRS:

* Azure Storage upprätthåller en skrivskyddad kopia av data du lagrar i din primära region i en sekundär region. Som nämnts ovan anger lagringstjänsten platsen för den sekundära regionen.

* Skrivskyddad kopia är [överensstämmelse](https://en.wikipedia.org/wiki/Eventual_consistency) med data i den primära regionen.

* För blobbar, tabeller och köer, kan du fråga den sekundära regionen för en *tid för senaste synkronisering* värde som anger när den senaste replikeringen från primär till sekundär region inträffade. (Detta stöds inte för Azure-filer som inte har RA-GRS redundans just nu.)

* Du kan använda Storage-klientbiblioteket för att interagera med data i den primära eller sekundära regionen. Du kan också omdirigera diskläsningsbegäranden automatiskt till den sekundära regionen om tidsgränsen uppnås för en läsbegäran till den primära regionen.

* Om det finns ett större problem som påverkar tillgängligheten för data i den primära regionen, kan Azure-teamet utlösa en geo-redundans, då DNS-posterna pekar på den primära regionen kommer att ändras för att peka på den sekundära regionen.

* Om geo-redundans inträffar kommer Azure Välj en ny sekundär plats, replikerar data till platsen och sedan sekundär DNS-posterna pekar på den. Sekundär slutpunkt blir otillgänglig tills lagringskontot är klar replikeras. Mer information finns [vad du ska göra om ett Azure Storage-avbrott inträffar](https://docs.microsoft.com/azure/storage/storage-disaster-recovery-guidance).

## <a name="application-design-considerations-when-using-ra-grs"></a>Designöverväganden för programmet när du använder RA-GRS

Syftet med den här artikeln är att visa dig hur du utformar ett program som fortsätter att fungera (i en begränsad kapacitet) även om en större katastrof på primära datacenter. Du kan utforma ditt program för att hantera tillfälliga eller långvariga problem genom att läsa från den sekundära regionen när det är problem som stör läsning från den primära regionen. Om den primära regionen är tillgänglig igen tillbaka programmet till läsning från den primära regionen.

### <a name="using-eventually-consistent-data"></a>Med hjälp av överensstämmelse data

Föreslagen lösning förutsätter att den är godkänd för att returnera kan vara inaktuella data till det anropande programmet. Eftersom data i den sekundära regionen är överensstämmelse, är det möjligt att den primära regionen kan bli oåtkomlig innan en uppdatering till den sekundära regionen är klar replikeras.

Anta exempelvis att kunden skickar en uppdatering har, men den primära regionen misslyckas innan uppdateringen har spridits till den sekundära regionen. När du frågar kunden att läsa data tillbaka, får han inaktuella data från den sekundära regionen i stället för den uppdaterade informationen. När du skapar ditt program, måste du bestämma om detta är acceptabelt och hur du kommer meddelandet kunden. 

Senare i den här artikeln beskrivs hur du kontrollerar synkronisering senast för sekundära data för att kontrollera om sekundärt är uppdaterad.

### <a name="handling-services-separately-or-all-together"></a>Hantera tjänster separat eller alla tillsammans

Även osannolik, är det möjligt för en tjänst som ska vara tillgänglig, medan andra tjänster som är fortfarande fungerar. Du kan hantera återförsök och skrivskyddat läge för varje service separat (BLOB, köer, tabeller) och du kan hantera försök Allmänt för storage-tjänster tillsammans.

Till exempel om du använder köer och blobbar i ditt program, kan du välja att placera i separat kod för att hantera återförsökbart fel för var och en av dessa. Sedan om du får ett nytt försök från blob-tjänsten, men kötjänsten fortfarande fungerar, att endast del av programmet hanterar BLOB påverkas. Om du vill hantera alla storage service återförsök Allmänt och ett anrop till blob-tjänsten returnerar ett återförsökbart fel, påverkas förfrågningar till både blob-tjänsten och kötjänsten.

Slutligen beror detta på komplexiteten i ditt program. Du behöver inte hantera felen av tjänsten, men i stället för att omdirigera begäranden för storage-tjänster till den sekundära regionen och kör programmet i skrivskyddat läge när du upptäcker ett problem med storage-tjänst i den primära regionen.

### <a name="other-considerations"></a>Andra överväganden

Dessa är de överväganden som diskuteras i resten av den här artikeln.

*   Hantera återförsök för läsbegäranden mönster för strömbrytare

*   Slutligen konsekventa data och tid för senaste synkronisering

*   Testning

## <a name="running-your-application-in-read-only-mode"></a>Kör ditt program i skrivskyddat läge

Om du vill använda RA-GRS-lagring måste kunna hantera både misslyckade begäranden för skrivskyddade och gick inte att uppdateringsbegäranden (uppdatering i det här fallet innebörd infogningar, uppdateringar och borttagningar). Om de primära Datacenter misslyckas, kan du läsa begäranden kan omdirigeras till sekundära datacentret. Dock kan omdirigeras begäranden om att uppdatera till sekundärt eftersom sekundärt är skrivskyddad. Därför måste du utforma programmet att köras i skrivskyddat läge.

Du kan till exempel ange en flagga som kontrolleras innan alla begäranden om att uppdatera skickas till Azure Storage. När en update-begäranden gäller, kan du hoppa över den och returnera ett lämpligt svar till kunden. Du kanske även vill inaktivera vissa funktioner helt och hållet förrän problemet har lösts och meddela användarna att dessa funktioner är inte tillgänglig för tillfället.

Om du vill hantera fel för varje service separat behöver du också hantera möjligheten att köra programmet i skrivskyddat läge av tjänsten. Du kan till exempel ha skrivskyddad flaggor för varje tjänst som kan aktiveras och inaktiveras. Du kan sedan hantera flaggan i lämpliga platser i din kod.

Att köra programmet i skrivskyddat läge har en annan sida fördel – den ger dig möjlighet att kontrollera begränsade funktioner under en Programuppgradering för viktiga. Du kan utlösa programmet att köras i skrivskyddat läge och peka på det sekundära datacentret säkerställer att ingen kommer åt data på den primära regionen medan du gör uppgraderingar.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Hantera uppdateringar när den körs i skrivskyddat läge

Det finns många sätt att hantera begäranden om att uppdatera när du kör i skrivskyddat läge. Vi kommer inte täcker detta utförligt, men i allmänhet det finns ett par av mönster som du vill.

1.  Du kan svara på din användare och berätta om du för närvarande inte accepterar uppdateringar. Ett system för hantering av kan till exempel möjligt för kunder att komma åt kontaktinformation utan att göra uppdateringar.

2.  Du kan placera dina uppdateringar i en annan region. I så fall skulle du skrivåtgärder väntar på att uppdateras till en kö i en annan region och har ett sätt att bearbeta begäranden när den primära datacentralen är online igen. I det här scenariot bör du låta kunder vet att den begärda uppdateringen är i kö för senare bearbetning.

3.  Du kan skriva dina uppdateringar till ett lagringskonto i en annan region. När den primära datacentralen är online igen kan du låta ett sätt att slå samman dessa uppdateringar till primära data, beroende på strukturen för data. Om du skapar separata filer med en stämpel för datum/tid i namnet kopiera du filerna till den primära regionen. Detta fungerar för vissa arbetsbelastningar som till exempel loggnings- och iOT-data.

## <a name="handling-retries"></a>Hantera nya försök

Hur vill du veta vilka fel återförsökbart? Detta bestäms av storage-klientbiblioteket. Ett 404-fel (Det gick inte att hitta resurs) är exempelvis inte återförsökbart eftersom du försöker den inte är sannolikt att lyckas. Ett fel 500 är å andra sidan återförsökbart eftersom det är ett serverfel och det kan vara ett övergående problem. Mer information finns i [öppna källkoden för klassen ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) i storage-klientbiblioteket för .NET. (Leta efter metoden ShouldRetry).

### <a name="read-requests"></a>Läs begäranden

Läs begäranden kan omdirigeras till den sekundära lagringsplatsen om det finns ett problem med primära lagringsplatsen. Som beskrivs ovan i [med slutligen konsekventa Data](#using-eventually-consistent-data), måste det vara godkänd för tillämpningsprogrammet för att kunna läsa inaktuella data. Om du använder storage-klientbibliotek för åtkomst till RA-GRS data, kan du ange beteendet för en läsbegäran försök igen genom att ange ett värde för den **LocationMode** egenskapen till ett av följande:

*   **PrimaryOnly** (standard)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

När du anger den **LocationMode** till **PrimaryThenSecondary**, om den ursprungliga skrivskyddade begäranden till primära slutpunkten misslyckas med ett återförsökbart fel klienten gör automatiskt en annan läsbegäran till sekundär slutpunkten. Om felet är en tidsgräns för servern, har klienten att vänta på tidsgräns att gälla innan den får ett återförsökbart fel från tjänsten.

Det finns två scenarier att tänka på när du beslutar hur man svarar på ett återförsökbart fel inträffade i princip:

*   Det här är en isolerad problem och efterföljande förfrågningar till den primära slutpunkten kommer inte att returnera ett återförsökbart fel inträffade. Ett exempel på där detta kan inträffa är när det är ett tillfälligt fel.

    I det här scenariot, det finns inga betydande prestandaproblem i med **LocationMode** inställd på **PrimaryThenSecondary** som det här inträffar bara sällan.

*   Detta är ett problem med minst en av lagringstjänsterna i den primära regionen och alla efterföljande förfrågningar till den tjänsten i den primära regionen kommer sannolikt att returnera återförsökbart fel under en tidsperiod. Ett exempel på detta är om den primära regionen är helt tillgänglig.

    I det här scenariot finns det en prestandaförsämring eftersom alla skrivskyddade förfrågningar kommer först försöka primära slutpunkten, vänta tills tidsgränsen ska upphöra att gälla och sedan växla till sekundär slutpunkten.

För dessa scenarier kan du identifiera att det är ett pågående problem med primära slutpunkten och skicka alla Läs begäranden direkt till den sekundära slutpunkten genom att ange den **LocationMode** egenskapen **SecondaryOnly**. För tillfället bör du också ändra programmet att köras i skrivskyddat läge. Den här metoden kallas den [strömbrytare mönster](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Begäranden om att uppdatera

Strömbrytare mönster kan också användas för att uppdatera begäranden. Dock omdirigeras inte begäranden om att uppdatera till sekundära lagringsplatsen, vilket är skrivskyddad. För sådana begäranden, bör du lämna den **LocationMode** egenskapen **PrimaryOnly** (standard). Du kan tillämpa ett mått på begäran – till exempel 10 fel i en rad – och när din tröskelvärdet har uppnåtts växla programmet till skrivskyddat läge för att hantera dessa fel. Du kan använda samma metoder för att returnera för att uppdatera läge som de som beskrivs nedan i nästa avsnitt om strömbrytare mönstret.

## <a name="circuit-breaker-pattern"></a>Mönster för strömbrytare

Hur du använder mönstret strömbrytare i ditt program kan förhindra att den du försöker en åtgärd som troligen kommer att misslyckas flera gånger. Det innebär att du kan fortsätta att köras i stället för tar tid medan åtgärden försöks exponentiellt. Dessutom upptäcks när felet har korrigerats som programmet kan försöka igen.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Hur du implementerar mönstret strömbrytare

Du kan övervaka hur ofta klienten påträffar ett återförsökbart fel för att identifiera att det finns en pågående problem med en primär slutpunkt. Eftersom varje fall skiljer sig, ha besluta om tröskelvärdet som du vill använda för beslutet kan växla till sekundär slutpunkten och kör programmet i skrivskyddat läge. Du kan till exempel välja att utföra växeln om det finns 10 fel i en rad med ingen lyckade. Ett annat exempel är att byta 90% av begäranden i en 2-minutersperiod misslyckas.

För det första scenariot kan du bara räkna felen och om det finns en fungerande innan det nådde maximum, ange antalet tillbaka till noll. För det andra scenariot är ett sätt att implementera den att använda MemoryCache-objekt (i .NET). För varje begäran lägga till en CacheItem cache, ange värdet till fungerande (1) eller misslyckas (0) och ange förfallotiden till 2 minuter från nu (eller vad den tid begränsningen är). När en post giltighetstid har uppnåtts, tas posten bort automatiskt. Detta ger ett rullande fönster 2 minuter. Varje gång du gör en begäran till tjänsten storage använda du först en Linq-fråga över MemoryCache-objektet för att beräkna procent lyckades genom att summera värdena och dividera med antalet. När procent lyckade hamnar under tröskelvärdet för (till exempel 10%), ange den **LocationMode** -egenskapen för Läs-begäranden till **SecondaryOnly** och växla till program i skrivskyddat läge innan du fortsätter.

Tröskelvärde för fel som används för att avgöra när du ska göra växeln kan variera från tjänsten i ditt program, så du bör överväga att göra dem till parametrar. Det är också där du vill hantera återförsökbart fel från varje service separat eller som en, vilket beskrivs ovan.

Ett annat övervägande är hur du hanterar flera instanser av ett program och vad du ska göra när du identifiera återförsökbart fel i varje instans. Du kan till exempel ha 20 virtuella datorer som körs med samma program lästes in. Ska du hantera varje instans separat? Om en instans startar problem kan du vill begränsa svar på samma sätt som en instans eller vill du försöka att ha svara alla instanser på samma sätt som när en instans har ett problem? Hanteringen av instanserna separat är mycket enklare än om du försöker att samordna svaret över dem, men hur du gör detta beror på ditt program arkitektur.

### <a name="options-for-monitoring-the-error-frequency"></a>Alternativ för att övervaka frekvens för fel

Det finns tre huvudsakliga alternativ för att övervaka antalet försök i den primära regionen för att avgöra när du ska växla till den sekundära regionen och ändra programmet att köras i skrivskyddat läge.

*   Lägger till en hanterare för den [ **försöker på nytt** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) händelsen på den [ **OperationContext** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) objekt som du skickar till ditt lagringsutrymme begär – detta är den metod som visas i den här artikeln och används i den medföljande exemplet. Dessa händelser eller när klienten försöker en begäran, så att du kan spåra hur ofta klienten påträffar ett återförsökbart fel på en primär slutpunkt.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   I den [ **Evaluate** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) metod i en egen återförsöksprincip du kan köra anpassad kod när ett nytt försök görs. Förutom registrering när ett nytt försök händer kan detta även ger dig möjlighet att ändra funktionssättet för försök igen.

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

*   Den tredje metoden är att implementera en anpassad övervakning komponent i ditt program som pingar kontinuerligt primära lagringsplatsen slutpunkten med dummy diskläsningsbegäranden (till exempel läsa en liten blob) att fastställa dess hälsa. Detta kan ta några resurser, men inte mycket. När ett problem har identifierats som når din tröskelvärde kan du sedan utföra växla till **SecondaryOnly** och skrivskyddat läge.

Någon gång kommer du vill växla tillbaka till med hjälp av den primära slutpunkten och tillåta uppdateringar. Om du använder en av de första två metoderna ovan kan du helt enkelt växla tillbaka till primära slutpunkt och aktivera uppdateringsläge när ett godtyckligt valda tid eller antal åtgärder har utförts. Sedan kan du låta den gå igenom logik för omprövning igen. Om problemet har korrigerats, fortsätter den att använda primära slutpunkt och tillåta uppdateringar. Om det finns fortfarande ett problem, växlar en gång tillbaka till sekundär slutpunkt och skrivskyddat läge efter misslyckade de kriterier som du har ställt in.

För scenariot med tredje när pinga primära lagringsplatsen slutpunkten blir lyckade igen, du kan utlösa växla tillbaka till **PrimaryOnly** och fortsätta att tillåta uppdateringar.

## <a name="handling-eventually-consistent-data"></a>Hantering av överensstämmelse data

RA-GRS fungerar genom att replikera transaktioner från den primära servern till den sekundära regionen. Den här replikeringen garanterar att data i den sekundära regionen är *överensstämmelse*. Detta innebär att alla transaktioner i den primära regionen kommer slutligen att visas i den sekundära regionen, men att det kan finnas en fördröjning innan de kan visas och att det är inte säkert transaktioner ankommer i den sekundära regionen i samma ordning som som de ursprungligen tillämpades på den primära regionen. Om transaktionerna kommer till den sekundära regionen utanför måste du *kan* Överväg att dina data i den sekundära regionen är i ett inkonsekvent tillstånd förrän tjänsten fångar.

I följande tabell visas ett exempel på vad som händer när du uppdaterar information om en anställd att göra hon medlem i den *administratörer* roll. För det här exemplet detta kräver att du uppdaterar den **medarbetare** entiteten och uppdatera en **administratörsroll** entitet med en beräkning av det totala antalet administratörer. Observera hur uppdateringarna tillämpas i ordning i den sekundära regionen.

| **Tid** | **Transaktionen**                                            | **Replikering**                       | **Tid för senaste synkronisering** | **Resultat** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transaktionen A: <br> Infoga medarbetare <br> entiteten i primär |                                   |                    | Transaktionen A infogas primära,<br> replikeras inte ännu. |
| T1       |                                                            | Transaktionen A <br> replikeras till<br> sekundär | T1 | Transaktionen A replikeras till sekundär. <br>Synkronisera senast uppdaterat.    |
| T2       | Transaktionen B:<br>Uppdatering<br> medarbetare entitet<br> i primära  |                                | T1                 | Transaktionen skrivs till primära B<br> replikeras inte ännu.  |
| T3       | Transaktionen C:<br> Uppdatering <br>Administratören<br>rollentiteten i<br>primär |                    | T1                 | Transaktionen skrivs till primära C<br> replikeras inte ännu.  |
| *T4*     |                                                       | Transaktionen C <br>replikeras till<br> sekundär | T1         | Transaktionen C replikeras till sekundär.<br>LastSyncTime inte uppdateras eftersom <br>transaktionen B har ännu inte replikerats.|
| *T5*     | Läs entiteter <br>från sekundär                           |                                  | T1                 | Inaktuella värdet för medarbetare hämtas <br> entiteten eftersom transaktionen B har inte <br> replikerade ännu. Du får det nya värdet för<br> administratören rollentiteten eftersom C har<br> replikeras. Tid för senaste synkronisering har fortfarande inte<br> har uppdaterats eftersom transaktionen B<br> har inte replikeras. Du kan se den<br>administratören rollentiteten är inkonsekvent <br>eftersom entiteten tidsvärdet efter <br>Tid för senaste synkronisering. |
| *T6*     |                                                      | Transaktionen B<br> replikeras till<br> sekundär | T6                 | *T6* – alla transaktioner via C har <br>har replikerats tid för senaste synkronisering<br> har uppdaterats. |

I det här exemplet förutsätter att klienten växlar till läsning från den sekundära regionen på T5. Det kan läsa den **administratörsroll** entitet just nu, men enheten innehåller ett värde för antalet administratörer som inte stämmer överens med antalet **medarbetare** entiteter som är markerade som administratörer på den sekundära regionen just nu. Klienten kan bara visa det här värdet med risk för att den är inkonsekvent information. Du kan också klienten kan försöka fastställa som den **administratörsroll** är i ett eventuellt inkonsekvent tillstånd eftersom uppdateringar har hänt fel ordning och informera användaren om detta.

Att identifiera att den har potentiellt inkonsekventa data, att klienten kan använda värdet för den *tid för senaste synkronisering* att du kan få när som helst genom att fråga en storage-tjänst. Detta visar den tid då data i den sekundära regionen senast konsekvent och när tjänsten har använt alla transaktioner innan den punkten i tid. I exemplet ovan, när tjänsten infogar den **medarbetare** entitet i den sekundära regionen tid för senaste synkronisering är inställd på *T1*. Det förblir *T1* förrän tjänstuppdateringar av **medarbetare** entitet i den sekundära regionen när den är inställd på *T6*. Om klienten hämtar den tid för senaste synkronisering när det läser entiteten vid *T5*, den kan jämföra med tidsstämpeln på enheten. Om tidsstämpeln på entiteten är senare än tidpunkten för senaste synkronisering sedan entiteten är i ett eventuellt inkonsekvent tillstånd och du kan ta allt är lämplig åtgärd för programmet. Det här fältet kräver att du vet när den senaste uppdateringen till primärt slutfördes.

## <a name="testing"></a>Testning

Det är viktigt att testa att programmet fungerar som förväntat när återförsökbart fel påträffas. Till exempel behöver du testa att programmet växlar till sekundärt och i skrivskyddat läge när den identifierar ett problem och växlar tillbaka när den primära regionen blir tillgänglig igen. Om du vill göra detta måste behöver du ett sätt att simulera återförsökbart fel och styra hur ofta de inträffar.

Du kan använda [Fiddler](http://www.telerik.com/fiddler) att komma åt och ändra HTTP-svar i ett skript. Det här skriptet kan identifiera svar som kommer från din primära slutpunkt och ändra HTTP-statuskoden till ett Storage-klientbiblioteket känner igen som ett återförsökbart fel inträffade. Det här kodstycket visar ett enkelt exempel på ett Fiddler skript som fångar upp svar läsbegäranden mot den **employeedata** tabell för att returnera status 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Du kan utöka det här exemplet för att komma åt ett brett spektrum av begäranden och bara ändra det **responseCode** på några av dem att bättre simulera ett verkligt scenario. Mer information om hur du anpassar Fiddler skript finns [ändra en begäran eller ett svar](http://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) i Fiddler-dokumentationen.

Om du har gjort tröskelvärden för växling av ditt program till skrivskyddat läge konfigurerbara, är det lättare att testa beteende med icke-produktion transaktionsvolymer.

## <a name="next-steps"></a>Nästa steg

* Mer information om läsbehörighet Geo-redundans, inklusive ett annat exempel på hur LastSyncTime ställs finns [Windows Azure-lagringsalternativ redundans och Geo-Redundant lagring med läsbehörighet](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Ett fullständigt exempel som visar hur du gör växla fram och tillbaka mellan de primära och sekundära slutpunkterna, se [Azure-exempel – med RA-GRS lagring strömbrytare mönstret](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
