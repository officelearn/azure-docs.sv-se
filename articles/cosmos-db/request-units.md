---
title: Enheter för programbegäran och beräkna dataflödet – Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur du förstår, ange och beräkna krav för begäran-enhet i Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rimman
ms.openlocfilehash: 66beeb2cc724f75d17a4c155f1cdb888153e8fbf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286773"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är Microsoft globalt distribuerad databastjänst. Du behöver inte hyr virtuella datorer, distribuera programvara eller övervaka databaser med Azure Cosmos DB. Azure Cosmos DB är drivs och kontinuerligt övervakas av Microsofts främsta tekniker att leverera i världsklass tillgänglighet, prestanda och dataskydd. Du kan komma åt dina data med hjälp av API: er föredrar, som den [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), och [tabell](table-introduction.md) API: er och en graf via den [Gremlin-API](graph-introduction.md). Alla API: er stöds alla internt. 

Valutan i Azure Cosmos DB är den *begäransenhet (RU)*. Med begäransenheter behöver du inte reservera Läs/Skriv-kapaciteter eller etablera CPU, minne och IOPS. Azure Cosmos DB stöder olika API: erna som har olika åtgärder, allt från enkla läser och skriver till komplexa graph-frågor. Eftersom inte alla begäranden som är lika med, tilldelas en normaliserad mängd begäransenheter baserat på mängden beräkning som krävs för att hantera begäran med begäranden. Antalet enheter för programbegäran för en åtgärd är deterministisk. Du kan spåra hur många enheter för programbegäran som förbrukas av alla åtgärder i Azure Cosmos DB via en svarshuvudet. 

För att tillhandahålla förutsägbar prestanda, Reservera dataflöde i enheter om 100 RU/sekund. Du kan [beräkna dataflödet behöver](request-units.md#estimating-throughput-needs) med hjälp av Azure Cosmos DB [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).

![Dataflöde Kalkylatorn][5]

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Vad är begäransenheter och begäran kostnader på Azure Cosmos DB?
* Hur anger jag begäran enhet kapacitet för en behållare eller en uppsättning behållare i Azure Cosmos DB?
* Hur beräknar jag mitt program begäransenhet behöver?
* Vad händer om jag överskrider begäran enhet kapacitet för en behållare eller en uppsättning behållare i Azure Cosmos DB?

Eftersom Azure Cosmos DB är en databas för flera modeller, är det viktigt att notera att den här artikeln gäller för alla datamodeller och API: er i Azure Cosmos DB. Den här artikeln använder allmänna villkor som *behållare* att Allmänt referera till en samling eller en graf och *objekt* att Allmänt referera till en tabell, dokument, noden eller entitet.

## <a name="request-units-and-request-charges"></a>Enheter för programbegäran och begära avgifter

Azure Cosmos DB ger snabb, förutsägbar prestanda genom att reservera resurser för att uppfylla programmets dataflödesbehov. Programmet belastning och åtkomst mönster ändras med tiden. Azure Cosmos DB kan du enkelt kan öka eller minska mängden reserverat dataflöde som är tillgängliga för ditt program.

Med Azure Cosmos DB är reserverat dataflöde anges som begäransenhet som bearbetades per sekund. Du kan tänka dig begäransenheter som dataflöde valuta. Du kan reservera ett antal garanterad begäransenheter ska vara tillgängliga för ditt program på basis av per sekund. Varje åtgärd i Azure Cosmos DB, såsom skriva dokument, utför en fråga och uppdatera ett dokument, förbrukar processor, minne och IOPS. Varje åtgärd medför det vill säga en avgift för begäran, uttryckt i begäransenheter. När du förstår de faktorer som påverkar programmets dataflödeskrav och begäran units, kan du köra programmet som kostnad effektivt som möjligt. 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Dataflöde isolering i globalt distribuerade databaser

Om du replikera din databas till fler än en region, tillhandahåller Azure Cosmos DB dataflöde isolering för att säkerställa att användningen av begäran-enheter i en region inte påverkar användningen av begäran-enheter i en annan region. Till exempel om du skriver data till en region och läsa data från en annan region, enheter för programbegäran som används för att genomföra någon skrivåtgärd i regionen A inte ta bort från enheter för programbegäran som används för läsning i regionen B. begäran enheter inte dela kro ss de regioner där du har distribuerat din databas. Varje region där databasen replikeras har fullständig antalet etablerade enheter för programbegäran. Mer information om global replikering finns i [att distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Överväganden för begäran-enhet
När du uppskatta antalet enheter för programbegäran att etablera, är det viktigt att tänka på följande variabler:

* **Objektet storlek**. När du ökar, ökar även antalet enheter för programbegäran som används för att läsa eller skriva data.
* **Objektet antal egenskaper**. Om vi antar att standard indexering av alla egenskaper, enheter som används för att skriva en ökning av dokument, noden eller entitet när egenskapen Antal ökar.
* **Datakonsekvens**. När du använder data konsekvensmodeller som starka eller föråldring förbrukas ytterligare enheter för programbegäran för att läsa objekt.
* **Indexerade egenskaper**. En princip för index på varje behållare avgör vilka egenskaper indexeras som standard. Du kan minska din begäran enhet förbrukning för skrivåtgärder genom att begränsa antalet indexerade egenskaper eller genom att aktivera lazy indexering.
* **Indexera dokument**. Som standard indexeras automatiskt varje objekt. Du kan använda färre enheter för programbegäran om du väljer att inte indexera vissa av dina objekt.
* **Fråga efter mönster**. Komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Antalet fråga resultat, antalet predikat, typen av predikat, hur många användardefinierade funktioner, storleken på källdata, och alla projektioner påverkar kostnaden för frågeåtgärder.
* **Skriptet användning**. Precis som med frågor, använda begäransenheter baserat på komplexiteten för åtgärder som utförs av lagrade procedurer och utlösare. Granska kostnad rubriken för att bättre förstå hur varje åtgärd förbrukar begäran enhet kapacitet när du utvecklar ditt program.

## <a name="estimating-throughput-needs"></a>Uppskattning dataflödesbehov
En begäransenhet är ett normaliserat mått på kostnaden för behandling. En enskild begäransenhet motsvarar bearbetningskapacitet som krävs för att läsa (via självsignerat länk eller ID) ett enskilt 1 KB-objekt som består av 10 unika egenskapsvärden (exklusive Systemegenskaper). En begäran om att skapa (insert), ersätta eller ta bort samma objekt förbrukar mer bearbetning från tjänsten och därmed kräver mer begäransenheter. 

> [!NOTE]
> 1 begäransenhet för ett 1 KB-objekt baslinjen motsvarar en enkel hämtning av självsignerat länk eller ID: T för objektet.
> 
> 

Här är till exempel en tabell som visar hur många enheter för programbegäran att etablera för artiklar med tre olika storlekar (1 KB, 4 KB och 64 KB) och till två olika prestandanivåer (500 läsningar/SEK + 100 skrivningar per sekund och 500 läsningar/SEK + 500 skrivningar per sekund). I det här exemplet anges dataintegriteten till **Session**, och indexprincip är inställt på **ingen**.

| Objektets storlek | Läsningar per sekund | Skrivningar per sekund | Enheter för programbegäran
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1 000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3 000 RU/s
| 4 KB | 500 | 100 | (500 * 1.3) + (100 * 7) = alltså 1 350 RU/s
| 4 KB | 500 | 500 | (500 * 1.3) + (500 * 7) = 4,150 RU/s
| 64 kB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/s
| 64 kB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/s


### <a name="use-the-request-unit-calculator"></a>Använda Kalkylatorn för begäran-enhet
Om du vill kan du finjustera din dataflöde uppskattningar, du kan använda en webbaserad [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner). Kalkylatorn kan beräkningen begäran enhet kraven för vanliga åtgärder, inklusive:

* Objektet skapas (skriver)
* Objekt läser
* Tar bort objekt
* Objekt-uppdateringar

Verktyget finns också stöd för att uppskatta lagringsbehov baserat på de exempelobjekt som du anger.

Använda verktyget:

1. Ladda upp en eller flera representativa objekt (till exempel ett exempel JSON-dokument).
   
    ![Överföra objekt till Kalkylatorn för begäran-enhet][2]
2. Ange det totala antalet objekt (till exempel dokument, rader eller hörn) som du vill lagra för att beräkna krav för datalagring.
3. Ange hur många skapa, läsa, uppdatera och ta borttagningsåtgärder som du behöver (på basis av per sekund). Ladda upp en kopia av exemplet objektet från steg 1 som inkluderar fältuppdateringar för vanliga för att beräkna units begäran för uppdateringsåtgärder för objektet. Till exempel om objektet uppdateringar normalt ändra två egenskaper med namnet *lastLogin* och *userVisits*, kopiera en exempel-objekt, uppdatera värden för dessa två egenskaper och sedan ladda upp det kopierade objektet.
   
    ![Ange dataflödeskrav för i begäran enhet Kalkylatorn][3]
4. Välj **Calculate**, och sedan granska resultaten.
   
    ![Begär enhet Kalkylatorn resultat][4]

> [!NOTE]
> Om du har objekttyper som skiljer sig avsevärt beroende på storleken och antalet indexerade egenskaper kan du ladda upp ett exempel på var och en *typ* typiska objektet till verktyget och sedan beräkna resultatet.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Använd svarshuvudet kostnad för Azure Cosmos DB-begäran
Varje svar från Azure Cosmos DB-tjänsten innehåller en anpassad rubrik (`x-ms-request-charge`) som innehåller enheter för programbegäran som används för en viss begäran. Du kan också komma åt den här rubriken via SDK: er för Azure Cosmos DB. I .NET-SDK **RequestCharge** är en egenskap för den **ResourceResponse** objekt. För frågor ger Azure Cosmos DB Data Explorer i Azure-portalen begäran kostnad information om utförda frågorna. Mer information om hur du hämtar och ange dataflöde med hjälp av olika flermodells API: er finns [ange och hämta dataflöde i Azure Cosmos DB](set-throughput.md) artikeln.

En metod för att uppskatta hur mycket reserverat dataflöde som krävs för programmet är att registrera begäran kostnaden för enheten förknippade med vanliga åtgärder mot ett representativt objekt som används av ditt program. Sedan beräkna antalet åtgärder som du hade tänkt utföra varje sekund. Tänk på att också mäta och innehåller vanliga frågor och användning av Azure Cosmos DB-skript.

> [!NOTE]
> Om du har objekttyper som skiljer sig avsevärt beroende på storleken och antalet indexerade egenskaper kan registrera gäller åtgärden begäran kostnaden för den enhet som hör till var *typ* för vanliga objekt.
> 
> 

Detta är till exempel de steg som du kan ta:

1. Registrera enhet-kostnad för begäran för att skapa (Infoga) ett typiskt-objekt. 
2. Post-begäran-avgiften för lyssnarenheten läsa ett typiskt-objekt.
3. Post-begäran-kostnaden för att uppdatera ett typiskt objekt för enheten.
4. Registrera enhet-kostnad för begäran av typisk, vanliga objekt frågor.
5. Registrera enhet-kostnad för begäran av eventuella egna skript (lagrade procedurer, utlösare, användardefinierade funktioner) som används i programmet.
6. Beräkna de nödvändiga begäransenheter givet det uppskattade antalet åtgärder som du räknar om du vill köra varje sekund.

## <a name="a-request-unit-estimate-example"></a>Ett exempel på begäran uppskattning
Överväg följande dokument, vilket är ungefär 1 KB stora:

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
> Dokument är minified i Azure Cosmos DB, så beräknas av systemet dokumentet ovan är något mindre än 1 KB.
> 
> 

I följande tabell visas ungefärlig begäran units för vanliga åtgärder på det här objektet. (Kostnaden för ungefärliga enheten förutsätter att konsekvensnivå konto har angetts till **Session** och att allt indexeras automatiskt.)

| Åtgärd | Avgiften för lyssnarenheten av begäran |
| --- | --- |
| Skapa objekt |~ 15 RU |
| Läs objekt |~ 1 RU |
| Fråga objekt efter ID |~2.5 RU |

I följande tabell visas ungefärlig begäran units för vanliga frågor som används i programmet:

| Fråga | Avgiften för lyssnarenheten av begäran | antal returnerade objekt |
| --- | --- | --- |
| Välj mat efter ID |~2.5 RU |1 |
| Välj foods efter tillverkare |~ 7 RU |7 |
| Välj av mat gruppen och ordning baserat på vikt |~ 70 RU |100 |
| Välj översta 10 livsmedel i en Matgrupp |~ 10 RU |10 |

> [!NOTE]
> Begäran units varierar beroende på antalet objekt som returneras.
> 
> 

Med den här informationen kan du beräkna begäran enhet kraven för det här programmet givet antalet åtgärder och frågor som du förväntar dig per sekund:

| Åtgärden/fråga | Uppskattade antal per sekund | Krävs för programbegäran |
| --- | --- | --- |
| Skapa objekt |10 |150 |
| Läs objekt |100 |100 |
| Välj foods efter tillverkare |25 |175 |
| Välj av Matgrupp |10 |700 |
| Välj Topp 10 |15 |150 totalt |

I detta fall använder du förväntar dig en genomsnittlig genomströmning krav i 1,275 RU/sekund. Avrundat upp till närmaste 100, skulle du etablerar 1,300 RU/sekund för det här programmet behållare (eller uppsättning behållare).

## <a id="RequestRateTooLarge"></a> Reserverat dataflöde överskreds i Azure Cosmos DB
Konsumtion för begäran om enheten utvärderas till en kostnad per sekund. För program som överstiger den etablerade begäranhastigheten, är förfrågningar rate-limited tills frekvensen sjunker under etablerat dataflöde-nivå. När en begäran är rate-limited, servern förebyggande syfte avslutas på begäran med `RequestRateTooLargeException` (HTTP-statuskod 429) och returnerar den `x-ms-retry-after-ms` rubrik. Huvudet anger hur lång tid i millisekunder som användaren måste vänta innan en ny begäran.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Om du använder .NET Client SDK och LINQ-frågor får i de flesta fall, du aldrig behöver bry dig om det här undantaget. Den aktuella versionen av klient-SDK för .NET fångar implicit svaret, respekterar det server angiven sidhuvudet retry-after och försöker automatiskt begäran. Om inte ditt konto är samtidigt som används av flera klienter, lyckas nästa återförsök.

Om du har mer än en klient kumulativt fungerar ovan blir förfrågningsfrekvensen, standardbeteendet för återförsök kan vara otillräckligt och klienten genererar ett `DocumentClientException` med status code 429 till programmet. I detta fall kan du överväga att hantera återförsöksbeteendet och logiken i ditt programs felhantering rutiner eller öka dataflödet som tillhandahållits för behållare (eller uppsättning behållare).

## <a name="next-steps"></a>Nästa steg
 
- Lär dig hur du [ange och hämta dataflöde i Azure Cosmos DB](set-throughput.md) med hjälp av Azure-portalen och SDK: er.
- Lär dig mer om [prestanda och skalningstester med Azure Cosmos DB](performance-testing.md).
- Läs mer om reserverat dataflöde med Azure Cosmos DB-databaser i [priser för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) och [partitionera data i Azure Cosmos DB](partition-data.md).
- Läs mer om Azure Cosmos DB i den [dokumentation för Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

