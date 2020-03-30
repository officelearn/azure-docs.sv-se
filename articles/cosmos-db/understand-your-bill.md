---
title: Förstå din Azure Cosmos DB-faktura
description: I den här artikeln beskrivs hur du förstår din Azure Cosmos DB-faktura med några exempel.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 5954c8eda370c0734985c47cfff6d073f5d76d17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258030"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Förstå Azure Cosmos DB-fakturan

Som en fullständigt hanterad molnbaserad databastjänst förenklar Azure Cosmos DB faktureringen genom att endast debitera för etablerat dataflöde och förbrukad lagring. Det finns inga ytterligare licensavgifter, maskinvaru-, verktygskostnader eller anläggningskostnader jämfört med lokala eller IaaS-värdbaserade alternativ. När du tänker på multiregionfunktionerna i Azure Cosmos DB ger databastjänsten en betydande minskning av kostnaderna jämfört med befintliga lokala eller IaaS-lösningar.

Med Azure Cosmos DB faktureras du varje timme baserat på etablerat dataflöde och förbrukad lagring. För det etablerade dataflödet är enheten för fakturering 100 RU/sek per timme, debiterad på $0.008 per timme, förutsatt att standard offentliga priser, se [sidan Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/). För förbrukad lagring debiteras du 0,25 USD per 1 GB lagringsutrymme och månad, se [sidan Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/). 

I den här artikeln använder vi några exempel som hjälper dig att förstå informationen på din månatliga faktura. De tal som visas i exemplen kan vara annorlunda om dina Azure Cosmos-containrar har ett annat etablerat dataflöde, om de sträcker sig över flera regioner eller körs under en annan period under månaden.

> [!NOTE]
> Fakturering är för någon del av en väggklocka timme, inte en 60 minuters varaktighet.

## <a name="billing-examples"></a>Exempel på fakturering

### <a name="billing-example---throughput-on-a-container-full-month"></a>Exempel på fakturering - dataflöde på en behållare (hel månad)

* Anta att du konfigurerar ett dataflöde på 1 000 RU/sek på en behållare, och det finns i 24 timmar * 30 dagar för månaden = totalt 720 timmar.  

* 1 000 RU/sek är 10 enheter om 100 RU/sek per timme för varje timme som behållarna finns (det vill vara 1 000/100 = 10). 

* Multiplicera 10 enheter per timme med kostnaden för $0.008 (per 100 RU/sec per timme) = $0.08 per timme. 

* Multiplicera $0.08 per timme med antalet timmar i månaden är lika med $0.08 * 24 timmar * 30 dagar = $57.60 för månaden.  

* Den totala månatliga räkningen kommer att visa 7.200 enheter (av 100 RU), som kommer att kosta $ 57,60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Exempel på fakturering - dataflöde på en behållare (delmånad)

* Låt oss anta att vi skapar en behållare med etablerat dataflöde på 2 500 RU/sek. Behållaren lever i 24 timmar under månaden (till exempel tar vi bort den 24 timmar efter att vi har skapat den).  

* Då får vi se 600 enheter på räkningen (2.500 RU / sek / 100 RU / sek / enhet * 24 timmar). Kostnaden kommer att vara $ 4,80 (600 enheter * $ 0,008 / enhet).

* Total faktura för månaden kommer att vara $ 4,80.

### <a name="billing-rate-if-storage-size-changes"></a>Faktureringsgrad om lagringsstorleken ändras

Lagringskapacitet faktureras i enheter av den maximala timmängden data som lagras, i GB, under en månadsperiod. Om du till exempel använde 100 GB lagringsutrymme under halva månaden och 50 GB för andra halvan av månaden debiteras du för motsvarande 75 GB lagringsutrymme under den månaden.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Faktureringshastighet när behållare eller en uppsättning behållare är aktiva i mindre än en timme

Du debiteras schablonpriset för varje timme som behållaren eller databasen finns, oavsett användning eller om behållaren eller databasen är aktiv i mindre än en timme. Om du till exempel skapar en behållare eller databas och tar bort den 5 minuter senare kommer fakturan att innehålla en timme.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Faktureringshastighet när dataflödet i en behållare eller databas skalas upp/ned

Om du ökar etablerat dataflöde klockan 9:30 från 400 RU/sek till 1 000 RU/sek och sedan sänker etablerat dataflöde klockan 10:45 tillbaka till 400 RU/sek, debiteras du i två timmar om 1 000 RU/sek. 

Om du ökar etablerat dataflöde för en behållare eller en uppsättning behållare vid 9:30 från 100-K RU/sek till 200 K RU/sek och sedan sänker etablerat dataflöde klockan 10:45 tillbaka till 100 K RU/sek, debiteras du i två timmar om 200 K RU/sek.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exempel på fakturering: flera behållare, var och en med dedikerat etablerat dataflödesläge

* Om du skapar ett Azure Cosmos-konto i östra US 2 med två behållare med etablerat dataflöde på 500 RU/sek respektive 700 RU/sek, skulle du ha ett totalt etablerat dataflöde på 1 200 RU/sek.  

* Du skulle debiteras 1,200/100 * $0.008 = $0.096/hour. 

* Om dina dataflödesbehov har ändrats, och du har ökat varje behållares kapacitet med 500 RU/sek samtidigt som du skapar en ny obegränsad behållare med 20 000 RU/sek, skulle din totala etablerade kapacitet vara 22 200 RU/sek (1 000 RU/sek + 1 200 RU/sek + 20 000RU/sek).  

* Din faktura skulle då ändras till: $ 0,008 x 222 = $ 1,776 / timme. 

* I en månad på 720 timmar (24 timmar * 30 dagar), om för 500 timmar etablerade genomströmning var 1.200 RU / sek och för de återstående 220 timmar etablerade genomströmningen var 22.200 RU / sek, din månatliga faktura visar: 500 x $ 0,096 / timme + 220 x $ 1,776 / timme = $ 438,72 / månad.

![Dedikerat exempel på dataflödesfaktura](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Exempel på fakturering: behållare med delat dataflödesläge

* Om du skapar ett Azure Cosmos-konto i östra US 2 med två Azure Cosmos-databaser (med en uppsättning behållare som delar dataflödet på databasnivå) med det etablerade dataflödet på 50 K RU/sek respektive 70 K RU/sek, skulle du ha ett totalt etablerat dataflöde på 120 K RU/sek.  

* Du skulle debiteras 1200 x $ 0,008 = $ 9,60 / timme. 

* Om ditt dataflödesbehov har ändrats och du ökade varje databass etablerade dataflöde med 10K RU/sek för varje databas, och du lägger till en ny behållare i den första databasen med dedikerat dataflödesläge på 15 K RU/sek till din delade dataflödesdatabas, skulle din totala etablerade kapacitet vara 155 K RU/sek (60 K RU/sek + 80 K RU/sek + 15 K RU/sek).  

* Din faktura skulle då ändras till: 1.550 * $ 0,008 = $ 12,40 / timme.  

* Under en månad på 720 timmar, om det för 300 timmar var etablerat genomströmning 120 K RU/sek och för de återstående 420 timmar som etablerats genomströmningen var 155 K RU/sek, din månatliga faktura kommer att visa: 300 x $ 9,60 / timme + 420 x $ 12,40 / timme = $ 2.880 + $ 5.208 = $ 8.088 / månad. 

![Exempel på faktura för delat dataflöde](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Faktureringsexempel med geo-replikering och multi-master  

Du kan lägga till/ta bort Azure-regioner var som helst i världen i ditt Azure Cosmos-databaskonto när som helst. Dataflödet som du har konfigurerat för olika Azure Cosmos-databaser och behållare reserveras i var och en av Azure-regioner som är associerade med ditt Azure Cosmos-databaskonto. Om summan av etablerat dataflöde (RU/sec) som konfigurerats i alla databaser och behållare i ditt Azure Cosmos-databaskonto (etablerat per timme) är T och antalet Azure-regioner som är associerade med ditt databaskonto är N, sedan är det totala etablerade dataflödet för en viss timme, för ditt Azure Cosmos-databaskonto, (a) konfigurerat med en enda skrivregion lika med T x N RU/sec och (b) konfigurerat med alla regioner som kan bearbeta skrivningar lika med T x (N+1) RU/sek. Etablerat dataflöde (enda skrivregion) kostar $0.008/timme per 100 RU/sek och etablerat dataflöde med flera skrivbara regioner (multi-master config) kostar $0.016/per timme per 100 RU/sec (se [sidan Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/)). Oavsett om det är dess enda skrivregion eller flera skrivregioner kan du läsa data från valfri region i Azure Cosmos DB.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exempel på fakturering: Azure Cosmos-konto med flera regioner, skriver en region

Anta att du har en Azure Cosmos-behållare i västra USA. Behållaren skapas med dataflöde 10K RU/sek och du lagrar 1 TB data den här månaden. Anta att du lägger till tre regioner (Östra USA, Norra Europa och Östasien) i ditt Azure Cosmos-konto, var och en med samma lagring och dataflöde. Din totala månadsräkning kommer att vara (förutsatt 30 dagar i en månad). Din faktura skulle vara följande: 

|**Objekt** |**Användning (månad)** |**Pris** |**Månadskostnad** |
|---------|---------|---------|-------|
|Dataflödesfaktura för container i västra USA      | 10K RU/sek * 24 * 30    |$0.008 per 100 RU/sek per timme   |$576|
|Genomströmningsnota för ytterligare tre regioner - Östra USA, Nordeuropa och Östasien       | 3 * 10K RU/sek * 24 * 30    |$0.008 per 100 RU/sek per timme  |$ 1.728|
|Lagringsfaktura för container i västra USA      | 250 GB    |$0.25/GB  |$62.50|
|Lagringsfaktura för ytterligare 3 regioner - Östra USA, Nordeuropa och Östasien      | 3 * 250 GB    |$0.25/GB  |$187.50|
|**Totalt**     |     |  |**$2 554**|

*Låt oss också anta att du skickar ut 100 GB data varje månad från behållaren i västra USA för att replikera data till Östra USA, Norra Europa och Östasien. Du debiteras för utgående enligt dataöverföringshastigheter.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exempel på fakturering: Azure Cosmos-konto med flera regioner, skriver flera regioner

Anta att du skapar en Azure Cosmos-behållare i västra USA. Behållaren skapas med dataflöde 10K RU/sek och du lagrar 1 TB data den här månaden. Anta att du lägger till tre regioner (Östra USA, Norra Europa och Östasien), var och en med samma lagring och dataflöde och du vill att du ska skriva till behållarna i alla regioner som är associerade med ditt Azure Cosmos-konto. Din totala månadsräkning kommer att vara (förutsatt 30 dagar i en månad) enligt följande:

|**Objekt** |**Användning (månad)**|**Pris** |**Månadskostnad** |
|---------|---------|---------|-------|
|Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)       | 10K RU/sek * 24 * 30    |$0.016 per 100 RU/sek per timme    |$1 152 |
|Genomströmningsnota för ytterligare tre regioner - östra USA, Nordeuropa och Östasien (alla regioner är skrivbara)        | (3 + 1) * 10K RU/sek * 24 * 30    |$0.016 per 100 RU/sek per timme   |$ 4.608 |
|Lagringsfaktura för container i västra USA      | 250 GB    |$0.25/GB  |$62.50|
|Lagringsfaktura för ytterligare 3 regioner - Östra USA, Nordeuropa och Östasien      | 3 * 250 GB    |$0.25/GB  |$187.50|
|**Totalt**     |     |  |**$ 6.010**|

*Låt oss också anta att du skickar ut 100 GB data varje månad från behållaren i västra USA för att replikera data till Östra USA, Norra Europa och Östasien. Du debiteras för utgående enligt dataöverföringshastigheter.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Faktureringsexempel: Azure Cosmos-konto med dataflöde på flera huvudenheter på databasnivå, inklusive dedikerat dataflödesläge för vissa behållare

Låt oss överväga följande exempel, där vi har ett Azure Cosmos-konto med flera regioner där alla regioner är skrivbara (config med flera huvudorcongen). För enkelhetens skull antar vi att lagringsstorleken förblir konstant och inte ändrar och utelämnar den här för att hålla exemplet enklare. Det etablerade genomströmflödet under månaden varierade enligt följande (förutsatt att 30 dagar eller 720 timmar): 

[0-100 timmar]:  

* Vi har skapat ett Azure Cosmos-konto i tre regioner (västra USA, östra USA, norra Europa), där alla regioner är skrivbara 

* Vi skapade en databas (D1) med delat dataflöde 10K RU/sek 

* Vi skapade en databas (D2) med delat dataflöde 30-K RU/sek och  

* Vi skapade en behållare (C1) med dedikerad dataflöde 20 K RU/sek 

[101-200 timmar]:  

* Vi skalas upp databas (D1) till 50 K RU/sek 

* Vi skalas upp databas (D2) till 70 K RU/sek  

* Vi raderade behållare (C1)  

[201-300 timmar]:  

* Vi skapade behållare (C1) igen med dedikerad dataflöde 20 K RU/sek 

[301-400 timmar]:  

* Vi har tagit bort en av regionerna från Azure Cosmos-kontot (# av skrivbara regioner är nu 2) 

* Vi skalas ner databas (D1) till 10K RU/sek 

* Vi skalas upp databas (D2) till 80 K RU /sek  

* Vi raderade behållare (C1) igen 

[401-500 timmar]:  

* Vi skalas ner databas (D2) till 10K RU/sek  

* Vi skapade behållare (C1) igen med dedikerad dataflöde 20 K RU/sek 

[501-700 timmar]:  

* Vi skalas upp databas (D1) till 20 K RU/sek  

* Vi skalas upp databas (D2) till 100 K RU /sek  

* Vi raderade behållare (C1) igen  

[701-720 timmar]:  

* Vi skalas ner databas (D2) till 50 K RU / sek  

Visuellt visas ändringarna i det totala etablerade genomströmningen under 720 timmar för månaden i figuren nedan: 

![Exempel på verkliga livet](./media/understand-your-bill/bill-example3.png)

Den totala månadsfakturan kommer att beräknas (förutsatt att 30 dagar/720 timmar i en månad) beräknas enligt följande:

|**Timmar**  |**RU/s** |**Objekt** |**Användning (timme)** |**Kostnad** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2 880 dollar  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |5 760 dollar  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |2 240 dollar  |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |6 720 dollar |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |1 440 $   |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2 880 dollar  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$ 1.280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |3 840 dollar  |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$ 7.680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbara)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Bill för ytterligare två regioner: Östra USA, Norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Total månadskostnad**  | |**$ 38.688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Faktureringsexempel med konton på den kostnadsfria nivån
Med Azure Cosmos DB-kostnadsfri nivå får du de första 400 RU/s och 5 GB lagringsutrymme i ditt konto gratis, som tillämpas på kontonivå. Alla RU/s och lagring utöver 400 RU/s och 5 GB kommer att faktureras enligt de ordinarie prisnivåerna per prissidan. På räkningen kommer du inte att se en avgift eller rad post för gratis 400 Ru / s och 5 GB, bara RU / s och lagring utöver vad som omfattas av fri nivå. 400 RU/s gäller alla typer av RU/s - etablerat dataflöde, autopilot (förhandsgranskning) och multimaster.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Faktureringsexempel - behållare eller databas med etablerat dataflöde
- Låt oss anta att vi skapar en databas eller behållare i ett konto på en kostnadsfri nivå med 400 RU/s och 5 GB lagringsutrymme.
- Din faktura visar inte någon avgift för den här resursen. Din tim- och månadskostnad blir 0 USD.
- Nu, låt oss anta i samma konto, lägger vi till en annan databas eller behållare med 1000 RU / s och 10 GB lagringsutrymme.
- Din faktura kommer nu att visa en avgift för 1000 RU / s och 10 GB lagringsutrymme. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Faktureringsexempel - behållare eller databas med autopilotdataflöde (förhandsgranskning)
- Låt oss anta att vi i ett konto på en kostnadsfri nivå skapar en databas eller behållare med autopilot aktiverad, med en maximal RU/s på 4000 RU/s. Denna resurs skalas automatiskt mellan 400 RU/s - 4000 RU/s. 
- Antag att i timme 1 till och med timme 10, är resursen minst 400 RU /s. Under timme 11 skalar resursen upp till 1000 RU/s och sedan tillbaka till 400 RU/s inom en timme.
- I timmar 1 till 10, kommer du att faktureras $ 0 för genomströmning, eftersom 400 RU / s täcktes av fri nivå. 
- I timme 11 kommer du att faktureras för en effektiv 1000 RU /s - 400 RU/s = 600 RU/s, eftersom detta är den högsta RU/s i timmen. Detta kommer att vara 6 enheter på 100 RU / s för timmen, så den totala genomströmningen kostnaden för timmen kommer att vara 6 enheter * $ 0,012 = $ 0,072. 
- All lagring utöver de första 5 GB kommer att faktureras till normala lagringshastigheter. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Exempel på fakturering - konto med flera regioner, en enda skrivregion
- Låt oss anta att vi i ett konto på en kostnadsfri nivå skapar en databas eller behållare med 1200 RU/s och 10 GB lagringsutrymme. Vi replikerar kontot till 3 regioner och vi har ett konto med en enda mall (enda skrivregion).
- Totalt, utan fri nivå, skulle vi faktureras för 3 * 1200 RU / s = 3600 RU / s och 3 * 10 GB = 30 GB lagringsutrymme.
- Med den kostnadsfria nivårabatten, efter att ha tagit bort 400 RU/s och 5 GB lagringsutrymme, debiteras vi för en effektiv 3200 RU/s (32 enheter) av etablerat dataflöde med en enda skrivregion och 25 GB lagringsutrymme.
- Den månatliga kostnaden för RU / s skulle vara: 32 enheter * $ 0,008 * 24 timmar * 31 dagar = $ 190,46. Den månatliga kostnaden för lagring skulle vara: 25 GB * 0,25 / GB = $ 6,25. Den totala kostnaden skulle vara $ 190,46 + $ 6,25 = $ 196,71.
- Obs: Om a-priset för RU/s eller lagring skiljer sig åt i regionerna, kommer den kostnadsfria nivån 400 RU/s och 5 GB att återspegla frekvensen för den region som kontot skapades i.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Exempel på fakturering – konto med flera regioner, flera mallar (flera skrivregioner)

Det här exemplet återspeglar [flerhuvudpriser](https://azure.microsoft.com/pricing/details/cosmos-db/) för konton som skapats efter den 1 december 2019. 
- Låt oss anta att vi i ett konto på en kostnadsfri nivå skapar en databas eller behållare med 1200 RU/s och 10 GB lagringsutrymme. Vi replikerar kontot till 3 regioner och vi har ett multi-master (multipel skrivregion) konto. 
- Totalt, utan fri nivå, skulle vi faktureras för 3 * 1200 RU / s = 3600 RU / s och 3 * 10 GB = 30 GB lagringsutrymme.
- Med den kostnadsfria nivårabatten, efter att ha tagit bort 400 RU/s och 5 GB lagringsutrymme, debiteras vi för en effektiv 3200 RU/s (32 enheter) av etablerat dataflöde med flera skrivregioner och 25 GB lagringsutrymme.
- Den månatliga kostnaden för RU / s skulle vara: 32 enheter * $ 0,016 * 24 timmar * 31 dagar = $ 380,93. Den månatliga kostnaden för lagring skulle vara: 25 GB * 0,25 / GB = $ 6,25. Den totala kostnaden skulle vara $ 380,93 + $ 6,25 = $ 387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktivt uppskatta din månadsfaktura  

Låt oss överväga ett annat exempel, där du vill proaktivt uppskatta din faktura innan månaden är. Du kan uppskatta din faktura på följande sätt:

|**Lagringskostnad** | |
|----|----|
|Genomsnittlig poststorlek (KB) |1 |
|Antal poster  |100,000,000  |
|Totalt lagringsutrymme (GB)  |100 |
|Månadskostnad per GB  |$0.25  |
|Förväntad månadskostnad för lagring   |$25.00  |

<br>

|**Kostnad för dataflöde** | | | |
|----|----|----|----|
|Åtgärdstyp| Begäranden/sek| Medel. RU/begäran| Ru:er behövs|
|Skriva| 100 | 5 | 500|
|Läsa| 400| 1| 400|

Totalt RU/sek: 500 + 400 = 900 Timkostnad: 900/100 * $0.008 = $0.072 Förväntad månadskostnad för dataflöde (förutsatt 31 dagar): $0.072 * 24 * 31 = $53.57

**Total månadskostnad**

Total månadskostnad = månadskostnad för lagring + månadskostnad för dataflöde Total månadskostnad = $25.00 + $53.57 = $78.57

*Priserna kan variera mellan olika regioner. Uppdaterad prissättning finns på sidan [Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fakturering med reserverad azure cosmos DB-kapacitet

Azure Cosmos DB reserverad kapacitet kan du köpa etablerat dataflöde i förväg (en reserverad kapacitet eller en reservation) som kan tillämpas på alla Azure Cosmos-databaser och behållare (för alla API eller datamodell) i alla Azure-regioner. Eftersom det etablerade dataflödespriset varierar per region, hjälper det att tänka på reserverad kapacitet som en monetär kredit som du har köpt med rabatt, som kan hämtas från för det etablerade dataflödet till respektive pris i varje region. Anta till exempel att du har ett Azure Cosmos-konto med en enda behållaretablering med 50 K RU/sek och globalt replikerade två regioner - Östra USA och Östra Japan. Om du väljer alternativet betala per du ska betalar du:  

* i östra USA: för 50 K RU/sek med en hastighet av 0,008 USD per 100 RU/sek i den regionen 

* i Östra Japan: för 50 K RU/sek med en hastighet av $0.009 per 100 RU/sec i den regionen

Din totala faktura (utan reserverad kapacitet) skulle vara (förutsatt 30 dagar eller 720 timmar): 

|**Regionen**| **Timpris per 100 RU/s**|**Enheter (RU/s)**|**Fakturerat belopp (per timme)**| **Fakturerat belopp (månadsvis)**|
|----|----|----|----|----|
|USA, östra|$0.008 |50 K|$4|2 880 dollar |
|Japan, östra|$0.009 |50 K| $4.50 |3 240 dollar |
|Totalt|||$8.50|6 120 $120 |

Låt oss tänka på att du har köpt reserverad kapacitet istället. Du kan köpa reserverad kapacitet för 100-K RU/sek till priset av $ 56.064 för ett år (med 20% rabatt), eller $ 6,40 per timme. Se prissättning för reserverad kapacitet på [sidan Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Kostnad för dataflöde (pay-as-you-go): 100.000 RU /sec/100 * $0.008/hour * 8760 timmar på ett år = $70,080 

* Kostnad för dataflöde (med reserverad kapacitet) $ 70.080 diskonterade på 20% = $ 56.064 

Vad du effektivt har köpt är en kredit på $ 8 per timme, för 100 K RU / sek med hjälp av listpriset i östra USA, till priset av $ 6,40 per timme. Du kan sedan dra ned från den här förbetalda dataflödesreservationen per timme för den etablerade dataflödeskapaciteten i alla globala Azure-regioner till respektive regionala listpriser som angetts för din prenumeration. I det här exemplet, där du etablerar 50 K RU/sek vardera i östra USA och Östra Japan, kommer du att kunna dra $8.00 i etablerat dataflöde per timme och debiteras övertaget på $0.50 per timme (eller $360/månad). 

|**Regionen**| **Timpris per 100 RU/s**|**Enheter (RU/s)**| **Fakturerat belopp (per timme)**| **Fakturerat belopp (månadsvis)**|
|----|----|----|----|----|
|USA, östra|$0.008 |50 K|$4|2 880 dollar |
|Japan, östra|$0.009 |50 K| $4.50 |3 240 dollar |
|||Pay-as-you-go|$8.50|$6120|
|Reserverad kapacitet som köpts|$0.0064 (20% rabatt) |100 RU/sek eller $8 kapacitet förköpt |-$8|-$5,760 |
|Nettoräkning|||0,50 USD |$360 |

## <a name="next-steps"></a>Efterföljande moment

Därefter kan du fortsätta att lära dig mer om kostnadsoptimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [hur Cosmos DB-prismodell är kostnadseffektiv för kunderna](total-cost-ownership.md)
* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
