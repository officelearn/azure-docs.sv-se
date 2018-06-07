---
title: Enheter för programbegäran & uppskatta genomströmning - Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur du förstår, ange och beräkna begäran enhet krav i Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: b8084008089225c11c8052c60be3afc152881040
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614840"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

[Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) är Microsofts globalt distribuerad flera modellen databas. Du behöver inte hyra ut virtuella datorer, distribuera programvara eller övervaka databaser med Azure Cosmos DB. Azure Cosmos-DB drivs och övervakas kontinuerligt av Microsoft översta tekniker för att leverera world klassen tillgänglighet, prestanda och data protection. Du kan komma åt dina data med hjälp av API: er som du själv väljer, till exempel den [SQL API](documentdb-introduction.md), [MongoDB API](mongodb-introduction.md), [tabell API](table-introduction.md), och ett diagram över den [Gremlin API](graph-introduction.md) - alla stöds. 

Azure Cosmos DB valuta är den **begära enhet (RU)**. Med RUs behöver du inte reserverad kapacitet för läsning och skrivning eller etablera CPU, minne och IOPS. Azure Cosmos-DB stöder ett antal API: er med olika åtgärder, från enkla läser och skriver till diagrammet komplexa frågor. Eftersom inte alla begäranden som är lika med de tilldelas en normaliserade mängd **programbegäran** baserat på mängden beräkning som är nödvändiga för att utföra begäran. Antalet enheter för begäran för en åtgärd är deterministisk och kan du spåra antalet begäran enheter som används av alla åtgärder i Azure Cosmos DB via en Svarsrubrik. 

Du måste reservera dataflöde i enheter om 100 RU/sekund för att ge förutsägbar prestanda. Du kan [beräkna din genomströmning måste](request-units.md#estimating-throughput-needs) med hjälp av Azure Cosmos DB [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).

![Genomströmning Kalkylatorn][5]

När du har läst den här artikeln kommer du att kunna svara på följande frågor:  

* Vad är frågeenheter och begäran tillägg i Azure Cosmos DB?
* Hur ange begäran enhet kapacitet för en behållare eller uppsättning behållare i Azure Cosmos DB?
* Hur jag beräkna måste mitt program begäran enhet?
* Vad händer om jag överskrider begäran enhet kapacitet för en behållare eller uppsättning behållare i Azure Cosmos DB?

Eftersom Azure Cosmos DB är en flera modellen databas. Det är viktigt att notera att den här artikeln gäller för alla datamodeller och API: er i Azure Cosmos DB. Den här artikeln använder allmänna villkor som *behållare* och en *objektet* att referera till en samling, diagram, eller en tabell och ett dokument, en nod eller en entitet Allmänt respektive.

## <a name="request-units-and-request-charges"></a>Frågeenheter och kostnader för begäran
Azure Cosmos-DB ger snabb och förutsägbar prestanda av *reservera* resurser för att uppfylla genomströmning för ditt program måste.  Eftersom program läsa in och komma åt mönster ändras över tiden, kan du enkelt öka eller minska mängden reserverat dataflöde som är tillgängliga för ditt program i Azure Cosmos DB.

Med Azure Cosmos DB anges reserverat dataflöde som frågeenheter som bearbetades per sekund. Du kan se frågeenheter som dataflöde valuta, där du *reservera* en mängd garanterad frågeenheter som är tillgängliga för ditt program med utgångspunkt från per sekund.  Varje åtgärd i Azure DB som Cosmos - skriva ett dokument, utför en fråga, uppdatera ett dokument - förbrukar CPU, minne och IOPS.  Det vill säga varje åtgärd har en *begära kostnad*, som uttrycks i *programbegäran*.  Förstå de faktorer som påverkar begäran enhet kostnader, tillsammans med ditt program genomströmning krav, kan du köra programmet som kostnad effektiv som möjligt. Data Explorer i Azure-portalen är också en fantastiska verktyg för att testa kärnan i en fråga.

Vi rekommenderar att komma igång med att titta på nedanstående video, där Azure Cosmos DB Programhanteraren Andrew Liu beskrivs frågeenheter.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Genomströmning isolering i globalt distribuerade databaser

När du har replikerats databasen till flera regioner, tillhandahåller Azure Cosmos DB genomströmning isolering för att säkerställa att RU användning i en region inte påverkar RU användning i en annan region. Till exempel om du skriva data till en region och läsa data från en annan region, RUs används för att utföra åtgärden i regionen *A* inte ta bort från RUs som används för läsning i regionen *B*. RUs delas inte mellan regioner där du har distribuerat. Varje region där databasen replikeras har fullständig antal RUs etableras. Mer information om globala replikering finns [hur du distribuerar data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Överväganden för begäran-enhet
När du uppskattar antalet begäran enheter att etablera, är det viktigt att beakta följande variabler:

* **Objektet storlek**. När du ökar antalet ökar även frågeenheter som används för att läsa eller skriva data.
* **Objektet antal egenskaper**. Under förutsättning att standard indexering av alla egenskaper för de enheter som används för att skriva en entitet-dokumentet/nod ökad när egenskapen Antal ökar.
* **Datakonsekvens**. När du använder konsekvenskontroll datamodeller, till exempel starka eller begränsas föråldrad förbrukas ytterligare frågeenheter för att läsa objekt.
* **Indexerade egenskaper**. En princip för index varje behållare avgör vilka egenskaper som indexeras som standard. Du kan minska din begäran Enhetsförbrukningen för skrivåtgärder genom att begränsa antalet indexerade egenskaper eller genom att aktivera lazy indexering.
* **Dokumentindexering**. Som standard indexeras automatiskt varje objekt. Du kan använda färre frågeenheter om du väljer att inte indexera vissa av dina artiklar.
* **Fråga mönster**. Komplexiteten i en fråga påverkar hur många enheter som begäran används för en åtgärd. Antalet frågeresultat, antalet predikat, uppbyggnad predikat, projektioner, antalet UDF: er och storleken på datakällan - påverkar alla kostnaden för frågor.
* **Script användning**.  Precis som med frågor, lagrade procedurer och utlösare kan du använda frågeenheter baserat på komplexiteten i åtgärder som utförs. När du utvecklar programmet inspektera kostnad begärandehuvudet för att bättre förstå hur varje åtgärd förbrukar begäran enhet kapacitet.

## <a name="estimating-throughput-needs"></a>Uppskattning behov för genomströmning
En begäran-enhet är ett normaliserat mått på kostnaden för begäranden. En enskild begäran enhet representerar bearbetningskapacitet som krävs för att läsa en enda 1 KB artikel bestående av 10 unika värden (exklusive Systemegenskaper) (via self länk eller id). En begäran om att skapa (insert), ersätta eller ta bort samma artikel förbrukar mer bearbetning från tjänsten och därmed mer frågeenheter.   

> [!NOTE]
> Baslinje för begäran om 1 enhet för en 1-KB-artikel motsvarar en enkel GET av self länk eller id för objektet.
> 
> 

Här är till exempel en tabell som visar hur många enheter som begäran ska etablera för artiklar med tre olika storlekar (1 KB 4 KB och 64 KB) och två olika prestandanivåer (500 läsningar per sekund + 100 skrivningar per sekund och 500 läsningar per sekund + 500 skrivningar per sekund). Dataintegriteten har konfigurerats med *Session*, och indexprincip har värdet *ingen*.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Objektets storlek</strong></p></td>
            <td valign="top"><p><strong>Läsningar per sekund</strong></p></td>
            <td valign="top"><p><strong>Skrivningar per sekund</strong></p></td>
            <td valign="top"><p><strong>Enheter för programbegäran</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 kB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 kB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Använda Kalkylatorn för begäran-enhet
För att hjälpa kunderna finjustera sina genomströmning uppskattningar, det är en webbaserad [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner) för att beräkna krav för begäran-enheter för vanliga åtgärder, inklusive:

* Objektet skapar (skriver)
* Läsningar av objektet
* Tar bort objekt
* Objektet uppdateringar

Verktyget omfattar också stöd för att uppskatta lagringsbehov baserat på exempelobjekt som du anger.

Med hjälp av verktyget är enkel:

1. Ladda upp en eller flera representativa objekt (t.ex, ett exempel JSON-dokument).
   
    ![Skicka objekt till begäran enhet Kalkylatorn][2]
2. Om du vill beräkna krav för datalagring, ange det totala antalet objekt (t.ex, dokument, rader eller formhörnen) du förväntar dig att lagra.
3. Ange antalet skapa, läsa, uppdatera och delete-åtgärder som du behöver (på grundval av per sekund). Överför en kopia av exemplet objekt från steg 1 ovan som innehåller fältuppdateringar för vanliga om du vill beräkna begäran enhet avgifterna för objektet uppdateringsåtgärder.  Till exempel om objektet uppdateringar vanligtvis ändra två egenskaper med namnet *lastLogin* och *userVisits*, sedan kopiera en exempel-objekt, uppdatera värdena för dessa två egenskaper och ladda upp det kopierade objektet.
   
    ![Ange krav för genomflöde i begäran enhet Kalkylatorn][3]
4. Klicka på Beräkna och granska resultatet.
   
    ![Begära enhet Kalkylatorn resultat][4]

> [!NOTE]
> Om du har objekttyper som varierar kraftigt när det gäller storlek och antalet indexerade egenskaper, sedan överföra ett exempel på varje *typen* typiska objektet till verktyget och sedan beräkna resultaten.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Använd Azure Cosmos DB begäran kostnad-svarshuvud
Varje svaret från tjänsten Azure Cosmos DB innehåller en anpassad rubrik (`x-ms-request-charge`) som innehåller frågeenheter som används för en viss begäran. Det här sidhuvudet är också tillgängligt via Azure Cosmos DB SDK. I SDK för .NET `RequestCharge` är en egenskap hos den `ResourceResponse` objekt.  För frågor ger Azure Cosmos DB Data Explorer i Azure portal begäran kostnad information för utförda frågor.

Med detta i åtanke är en metod för att uppskatta mängden reserverat dataflöde som krävs för programmet att registrera begäran enhet tillägget som är associerade med vanliga åtgärder som körs mot ett representativt objekt som används av ditt program och sedan beräkna den antal åtgärder som du vill utföra varje sekund.  Se till att mäta och innehåller vanliga frågor och Azure Cosmos DB skript samt användning.

> [!NOTE]
> Om du har objekttyper som skiljer sig avsevärt när det gäller storlek och antalet indexerade egenskaper som är associerade med varje tillämplig åtgärden begäran enhet kostnad registrera *typen* för vanliga objekt.
> 
> 

Exempel:

1. Registrerar begäran enhet kostnad för att skapa (Infoga) en typisk objektet. 
2. Registrera begäran enhet kostnad läsa vanliga objekt.
3. Registrera begäran enhet kostnad för att uppdatera en typisk objektet.
4. Registrera begäran enhetsavgift för vanliga, vanliga artikel frågor.
5. Registrera begäran enhet tillägget för alla anpassade skript (lagrade procedurer, utlösare, användardefinierade funktioner) kan användas av programmet
6. Beräkna de nödvändiga frågeenheter anges uppskattade antal åtgärder som du vill köra varje sekund.

## <a name="a-request-unit-estimate-example"></a>Ett exempel på begäran uppskattning
Överväg följande ~ 1 KB dokument:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Dokument är minified i Azure Cosmos-databasen så att systemet beräknas storleken på dokumentet ovan är något mindre än 1 KB.
> 
> 

I följande tabell visas ungefärlig begäran enhet kostnader för vanliga åtgärder på det här objektet (ungefärliga begäran enhet kostnad förutsätter att konsekvensnivå konto har angetts till *Session* och att alla objekt är automatiskt indexerade):

| Åtgärd | Begäran enhet kostnad |
| --- | --- |
| Skapa objekt |~ 15 RU |
| Läs objekt |~ 1 RU |
| Frågan objekt-ID: t |~2.5 RU |

Den här tabellen visas dessutom ungefärliga begäran enhet avgifter för vanliga frågor som används i programmet:

| Fråga | Begäran enhet kostnad | antal returnerade artiklar |
| --- | --- | --- |
| Välj mat-ID: t |~2.5 RU |1 |
| Välj livsmedel per tillverkare |~ 7 RU |7 |
| Välj av mat gruppen och ordning baserat på vikt |~ 70 RU |100 |
| Välj översta 10 livsmedel i en Matgrupp |~ 10 RU |10 |

> [!NOTE]
> RU avgifter varierar beroende på antalet objekt som returneras.
> 
> 

Med den här informationen kan du beräkna RU kraven för det här programmet får antalet åtgärder och frågor som du förväntar dig per sekund:

| Åtgärden-fråga | Uppskattat antal per sekund | Nödvändiga RUs |
| --- | --- | --- |
| Skapa objekt |10 |150 |
| Läs objekt |100 |100 |
| Välj livsmedel per tillverkare |25 |175 |
| Välj av Matgrupp |10 |700 |
| Välj Topp 10 |15 |150 totalt |

I detta fall kan du förvänta dig en genomsnittlig genomströmning krav på 1,275 RU/s.  Avrundas till närmaste 100, skulle du etablera 1 300 RU/s för det här programmet behållare (eller uppsättning behållare).

## <a id="RequestRateTooLarge"></a> Reserverat dataflöde överskreds i Azure Cosmos DB
Kom ihåg att begäran enhet förbrukning utvärderas med en hastighet per sekund. För program som överskrider den etablerade begäran enhet hastigheten har begäranden begränsad hastighet tills frekvensen sjunker under nivån dataflöde. När en begäran hämtar begränsad hastighet, servern förebyggande syfte avslutas på begäran med `RequestRateTooLargeException` (HTTP-statuskod 429) och returnerar det `x-ms-retry-after-ms` huvud som anger hur lång tid i millisekunder som användaren måste vänta innan du försöker begäran.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Om du använder klient-SDK för .NET och LINQ-frågor och sedan i de flesta fall måste du aldrig hantera detta undantag som den aktuella versionen av .NET-klient-SDK fångar implicit svaret respekterar server angetts försök igen efter rubriken och försöker den begäran om automatiskt. Om ditt konto används samtidigt av flera klienter, lyckas nästa försök.

Om du har mer än ett klientoperativsystem kumulativt högre begärandehastighet, försök standardbeteendet inte finns tillräckligt och klienten genereras en `DocumentClientException` med statusen code 429 till programmet. I detta fall kanske du vill överväga hantering försök beteende och logiken i ditt program felhantering rutiner eller öka dataflödet för behållaren (eller uppsättning behållare).

## <a name="next-steps"></a>Nästa steg
 
Mer information om hur du ställer och få dataflöde med hjälp av Azure-portalen och SDK finns:

* [Ange och hämta genomflöde i Azure Cosmos DB](set-throughput.md)

Utforska gärna dessa resurser om du vill veta mer om reserverat dataflöde med Azure Cosmos DB databaser kan:

* [Azure DB Cosmos-priser](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Partitionering data i Azure Cosmos DB](partition-data.md)

Mer information om Azure Cosmos DB finns Azure Cosmos DB [dokumentationen](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Om du vill komma igång med skalning och prestandatester med Azure Cosmos DB, se [prestanda och Skalningstester med Azure Cosmos DB](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

