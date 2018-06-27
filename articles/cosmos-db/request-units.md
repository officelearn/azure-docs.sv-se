---
title: Enheter för programbegäran och uppskatta genomströmning - Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur du förstår, ange och beräkna begäran enhet krav i Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 9021d0c3f650d64480f2881508d456ce98beab2a
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36961968"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

[Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) är Microsoft globalt distribuerad multimodel databas. Du behöver inte hyra ut virtuella datorer, distribuera programvara eller övervaka databaser med Azure Cosmos DB. Azure Cosmos-DB drivs och övervakas kontinuerligt av Microsoft översta tekniker för att leverera världsklass tillgänglighet, prestanda och dataskydd. Du kan komma åt dina data med hjälp av API: er som du själv väljer, som den [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), och [tabell](table-introduction.md) API: er och diagram via den [Gremlin API](graph-introduction.md). Alla API: er stöds alla internt. 

Azure Cosmos DB valuta är den *begäran enhet (RU)*. Med frågeenheter behöver du inte att reservera kapacitet för läsning och skrivning eller etablera CPU, minne och IOPS. Azure Cosmos-DB stöder olika API: er som har olika åtgärder, allt från enkla läser och skriver till diagrammet komplexa frågor. Eftersom inte alla begäranden som är lika har begäranden tilldelats ett normaliserat antal begäran enheter baserat på mängden beräkning som är nödvändiga för att utföra begäran. Antalet enheter för begäran för en åtgärd är deterministisk. Du kan spåra antalet frågeenheter som förbrukas av alla åtgärder i Azure Cosmos DB via en Svarsrubrik. 

För att ge förutsägbar prestanda, Reservera dataflöde i enheter av 100 RU per sekund. Du kan [beräkna din genomströmning måste](request-units.md#estimating-throughput-needs) med hjälp av Azure Cosmos DB [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).

![Genomströmning Kalkylatorn][5]

När du har läst den här artikeln kommer du att kunna svara på följande frågor:

* Vad är frågeenheter och begäran tillägg i Azure Cosmos DB?
* Hur ange begäran enhet kapacitet för en behållare eller uppsättning behållare i Azure Cosmos DB?
* Hur jag beräkna måste mitt program begäran enhet?
* Vad händer om jag överskrider begäran enhet kapacitet för en behållare eller uppsättning behållare i Azure Cosmos DB?

Eftersom Azure Cosmos DB är en multimodel databas, är det viktigt att notera att den här artikeln gäller för alla datamodeller och API: er i Azure Cosmos DB. Den här artikeln använder allmänna villkor som *behållare* att Allmänt referera till en samling eller diagram och *objektet* att Allmänt referera till en tabell, dokument, nod eller enhet.

## <a name="request-units-and-request-charges"></a>Frågeenheter och kostnader för begäran

Azure Cosmos-DB ger snabb och förutsägbar prestanda genom att reservera resurser för att uppfylla behov för genomströmning av ditt program. Programmet belastnings- och mönster ändras med tiden. Azure Cosmos-DB kan hjälpa dig att enkelt öka eller minska mängden reserverat dataflöde som är tillgängliga för ditt program.

Med Azure Cosmos DB anges reserverat dataflöde i begäran enhet som bearbetades per sekund. Du kan se frågeenheter som dataflöde valuta. Du kan reservera ett antal garanterad frågeenheter ska vara tillgängliga för ditt program på grundval av per sekund. Varje åtgärd i Azure Cosmos DB, inklusive skriva ett dokument, utför en fråga och uppdaterar ett dokument, förbrukar CPU, minne och IOPS. Varje åtgärd ådrar som är en avgift för begäran som anges i begäran enheter. När du förstår de faktorer som påverkar begäran enhet kostnader och genomströmning programkrav kan köra du programmet som kostnad effektiv som möjligt. 

För att hjälpa dig att komma igång, beskrivs Azure Cosmos DB Programhanteraren Andrew Liu frågeenheter i följande video: <br /><br />

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Genomströmning isolering i globalt distribuerade databaser

Om du replikerar databasen till mer än en region tillhandahåller Azure Cosmos DB genomströmning isolering för att säkerställa att begäran enhet användning i en region inte påverkar begäran enhet användning i en annan region. Till exempel om du skriver data till en region och läsa data från en annan region frågeenheter som används för att utföra åtgärden i region A inte ta bort från frågeenheter som används för läsning i region B. begäran enheter inte dela kro ss regioner där du har distribuerat din databas. Varje region där databasen replikeras har fullständig antal frågeenheter etableras. Mer information om globala replikering finns [hur du distribuerar data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Överväganden för begäran-enhet
När du uppskatta antalet frågeenheter att etablera, är det viktigt att tänka på följande variabler:

* **Objektet storlek**. När storlek ökar, ökar också antalet begäran enheter som används för att läsa eller skriva data.
* **Objektet antal egenskaper**. Under förutsättning att standard indexering av alla egenskaper för de enheter som används för att skriva ett dokument, en nod eller entiteten öka när egenskapen Antal ökar.
* **Datakonsekvens**. När du använder konsekvenskontroll datamodeller som starka eller begränsas föråldrad förbrukas ytterligare frågeenheter för att läsa objekt.
* **Indexerade egenskaper**. En princip för index varje behållare avgör vilka egenskaper som indexeras som standard. Du kan minska din begäran Enhetsförbrukningen för skrivåtgärder genom att begränsa antalet indexerade egenskaper eller genom att aktivera lazy indexering.
* **Dokumentindexering**. Som standard indexeras automatiskt varje objekt. Du kan använda färre frågeenheter om du väljer att inte indexera vissa av dina artiklar.
* **Fråga mönster**. Komplexiteten i en fråga påverkar hur många enheter som begäran används för en åtgärd. Antalet frågor som resultat, antalet predikat, vilka slags predikat, antalet användardefinierade funktioner, storleken på källdata, och alla projektioner påverkar kostnaden för frågor.
* **Script användning**. Precis som med frågor, lagrade procedurer och utlösare kan du använda frågeenheter baserat på komplexiteten i åtgärder som utförs. När du utvecklar programmet inspektera kostnad begärandehuvudet för att bättre förstå hur varje åtgärd förbrukar begäran enhet kapacitet.

## <a name="estimating-throughput-needs"></a>Uppskattning behov för genomströmning
En begäran-enhet är ett normaliserat mått på kostnaden för begäranden. En enskild begäran enhet representerar bearbetningskapacitet som krävs för att läsa (via self länk eller ID) ett enskilt objekt för 1 KB som består av 10 unika värden (exklusive Systemegenskaper). En begäran om att skapa (insert), ersätta eller ta bort samma artikel förbrukar mer bearbetning från tjänsten och därmed kräver mer frågeenheter. 

> [!NOTE]
> Baslinje för begäran om 1 enhet för en 1-KB-artikel motsvarar enkla GET av self länk eller ID för objektet.
> 
> 

Här är till exempel en tabell som visar hur många enheter som begäran ska etablera för artiklar med tre olika storlekar (1 KB 4 KB och 64 KB) och två olika prestandanivåer (500 läsningar per sekund + 100 skrivningar per sekund och 500 läsningar per sekund + 500 skrivningar per sekund). I det här exemplet dataintegriteten är inställd på **Session**, och indexprincip är inställd på **ingen**.

| Objektets storlek | Läsningar per sekund | Skrivningar per sekund | Enheter för programbegäran
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1 000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3 000 RU/s
| 4 KB | 500 | 100 | (500 * 1.3) + (100 * 7) = 1,350 RU/s
| 4 KB | 500 | 500 | (500 * 1.3) + (500 * 7) = 4,150 RU/s
| 64 kB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/s
| 64 kB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/s


### <a name="use-the-request-unit-calculator"></a>Använda Kalkylatorn för begäran-enhet
För att finjustera din genomströmning uppskattningar ska du använda en webbaserad [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner). Kalkylatorn hjälper uppskattningen begäran enhet kraven för vanliga åtgärder, inklusive:

* Objektet skapar (skriver)
* Läsningar av objektet
* Tar bort objekt
* Objektet uppdateringar

Verktyget omfattar också stöd för att uppskatta lagringsbehov baserat på exempelobjekt som du anger.

Du använder verktyget:

1. Ladda upp en eller flera representativa objekt (t.ex, ett exempel JSON-dokument).
   
    ![Skicka objekt till begäran enhet Kalkylatorn][2]
2. Ange det totala antalet objekt (t.ex, dokument, rader eller formhörnen) som du förväntar dig att lagra för att beräkna krav för datalagring.
3. Ange antalet skapa, läsa, uppdatera och delete-åtgärder som du behöver (på grundval av per sekund). Överför en kopia av exempel objekt från steg 1 som innehåller fältuppdateringar för vanliga om du vill beräkna begäran enhet avgifterna för objektet uppdateringsåtgärder. Till exempel om objektet uppdateringar vanligtvis ändra två egenskaper med namnet *lastLogin* och *userVisits*, kopiera en exempel-objekt, uppdatera värdena för dessa två egenskaper och sedan ladda upp det kopierade objektet.
   
    ![Ange krav för genomflöde i begäran enhet Kalkylatorn][3]
4. Välj **Calculate**, och granska resultatet.
   
    ![Begära enhet Kalkylatorn resultat][4]

> [!NOTE]
> Om du har objekttyper som skiljer sig avsevärt när det gäller storlek och antalet indexerade egenskaper kan överföra ett exempel på varje *typen* typiska objektet till verktyget och sedan beräkna resultaten.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Använd Azure Cosmos DB begäran kostnad-svarshuvud
Varje svaret från tjänsten Azure Cosmos DB innehåller en anpassad rubrik (`x-ms-request-charge`) som innehåller frågeenheter som används för en viss begäran. Du kan också komma åt det här sidhuvudet via Azure Cosmos DB SDK. I SDK för .NET **RequestCharge** är en egenskap hos den **ResourceResponse** objekt. För frågor ger Azure Cosmos DB Data Explorer i Azure portal begäran kostnad information för utförda frågor. Mer information om hur du hämtar och ange dataflöde med hjälp av olika flera modellen API: er finns [ange och hämta genomflöde i Azure Cosmos DB](set-throughput.md) artikel.

En metod för att uppskatta mängden reserverat dataflöde som krävs för programmet är att registrera begäran enhet tillägget som är associerade med vanliga åtgärder som körs mot ett representativt objekt som används av ditt program. Sedan beräkna antal åtgärder som du vill utföra varje sekund. Se till att även mäta och innehåller vanliga frågor och Azure Cosmos DB skript användning.

> [!NOTE]
> Om du har objekttyper som skiljer sig avsevärt när det gäller storlek och antalet indexerade egenskaper, registrera tillämpliga åtgärden begäran enhetsavgift som associeras med varje *typen* för vanliga objekt.
> 
> 

Till exempel är dessa de steg du kan vidta:

1. Registrerar begäran enhet kostnad för att skapa (Infoga) en typisk objektet. 
2. Registrera begäran enhet kostnad läsa vanliga objekt.
3. Registrera begäran enhet kostnad för att uppdatera en typisk objektet.
4. Registrera begäran enhetsavgift för vanliga, vanliga artikel frågor.
5. Registrera begäran enhet tillägget för alla anpassade skript (lagrade procedurer, utlösare, användardefinierade funktioner) som används i programmet.
6. Beräkna de nödvändiga frågeenheter anges uppskattade antal åtgärder som du vill köra varje sekund.

## <a name="a-request-unit-estimate-example"></a>Ett exempel på begäran uppskattning
Överväg följande dokumentet, vilket är ungefär 1 KB:

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
> Dokument är minified i Azure Cosmos-databasen, så beräknas av systemet dokumentet ovan är något mindre än 1 KB.
> 
> 

I följande tabell visas ungefärlig begäran enhet kostnader för vanliga åtgärder på det här objektet. (Ungefärliga begäran enhet kostnad förutsätter att konsekvensnivå konto har angetts till **Session** och att alla objekt indexeras automatiskt.)

| Åtgärd | Begäran enhet kostnad |
| --- | --- |
| Skapa objekt |~ 15 RU |
| Läs objekt |~ 1 RU |
| Frågan objekt-ID: t |~2.5 RU |

I följande tabell visas ungefärlig begäran enhet avgifter för vanliga frågor som används i programmet:

| Fråga | Begäran enhet kostnad | antal returnerade artiklar |
| --- | --- | --- |
| Välj mat-ID: t |~2.5 RU |1 |
| Välj livsmedel per tillverkare |~ 7 RU |7 |
| Välj av mat gruppen och ordning baserat på vikt |~ 70 RU |100 |
| Välj översta 10 livsmedel i en Matgrupp |~ 10 RU |10 |

> [!NOTE]
> Begäran enhet avgifter variera beroende på antalet objekt som returneras.
> 
> 

Med den här informationen kan du beräkna begäran enhet kraven för det här programmet får antalet åtgärder och frågor som du förväntar dig per sekund:

| Åtgärden-fråga | Uppskattat antal per sekund | Nödvändiga frågeenheter |
| --- | --- | --- |
| Skapa objekt |10 |150 |
| Läs objekt |100 |100 |
| Välj livsmedel per tillverkare |25 |175 |
| Välj av Matgrupp |10 |700 |
| Välj Topp 10 |15 |150 totalt |

I så fall måste du förväntar dig en genomsnittlig genomströmning behovet av 1,275 RU per sekund. Avrundas till närmaste 100, skulle du etablera 1,300 RU per sekund för det här programmet behållare (eller uppsättning behållare).

## <a id="RequestRateTooLarge"></a> Reserverat dataflöde överskreds i Azure Cosmos DB
Begäran enhet förbrukning utvärderas med en hastighet per sekund. För program som överskrider den etablerade begäran enhet hastigheten är-begäranden frekvensen begränsad förrän frekvensen sjunker under nivån dataflöde. När en begäran är begränsad hastighet, servern förebyggande syfte avslutas på begäran med `RequestRateTooLargeException` (HTTP-statuskod 429) och returnerar det `x-ms-retry-after-ms` huvud. Huvudet anger hur lång tid i millisekunder som användaren måste vänta innan du försöker begäran.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Om du använder klient-SDK för .NET och LINQ-frågor måste för den mesta du aldrig hantera detta undantag. Den aktuella versionen av klient-SDK för .NET fångar upp implicit svaret respekterar server angetts försök igen efter rubriken och återförsök automatiska begäran. Om ditt konto används samtidigt av flera klienter, lyckas nästa försök.

Om du har mer än ett klientoperativsystem kumulativt högre begärandehastighet, försök standardbeteendet kan vara otillräckliga och klienten genererar ett `DocumentClientException` med statusen code 429 till programmet. I detta fall kanske du vill överväga hantering försök beteende och logiken i ditt program felhantering rutiner eller öka dataflödet för behållare (eller uppsättning behållare).

## <a name="next-steps"></a>Nästa steg
 
- Lär dig hur du [ange och hämta genomflöde i Azure Cosmos DB](set-throughput.md) med hjälp av Azure-portalen och SDK: er.
- Lär dig mer om [prestanda och skalning testa med Azure Cosmos DB](performance-testing.md).
- Läs mer om reserverat dataflöde med Azure Cosmos DB databaser i [priser för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) och [partitionering data i Azure Cosmos DB](partition-data.md).
- Läs mer om Azure Cosmos DB i den [Azure Cosmos DB dokumentationen](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

