---
title: Förstå din faktura för Azure Cosmos DB
description: Den här artikeln beskriver hur du förstå din faktura för Azure Cosmos DB med några exempel.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 5df11eb4e1b759d66200c09cabc8752532d0873c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042163"
---
# <a name="understanding-your-azure-cosmos-db-bill"></a>Förstå din faktura för Azure Cosmos DB

Som en fullständigt hanterad molnbaserade databastjänst, Azure Cosmos DB förenklar fakturering genom att debitera bara för etablerat dataflöde och förbrukad lagring. Det finns inga ytterligare licensavgifter, maskinvara, verktyget kostnader eller anläggning kostnader jämfört med en lokal eller alternativ för IaaS-värd. När du överväger att samskapa region funktionerna i Azure Cosmos DB och ger databastjänsten en betydande minskning i kostnader jämfört med befintliga lokala eller IaaS-lösningar.

Du debiteras per timme baserat på etablerat dataflöde och förbrukad lagring med Azure Cosmos DB. För det etablerade dataflödet enheten för fakturering är 100 RU/SEK per timme, debiteras enligt $0.008 per timme, förutsatt att standardpriserna för offentlig, finns i den [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/). Förbrukad lagring du är faktureras $0.25 per 1 GB lagringsutrymme per månad, finns i den [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Den här artikeln använder några exempel för att hjälpa dig att förstå informationen som visas på den månatliga fakturan. De siffror som visas i exemplen kan skilja sig om Azure Cosmos-behållare har en annan mängd dataflödet som etableras, om de över flera regioner eller körs för en annan under en viss över en månad.

## <a name="billing-examples"></a>Faktureringsexempel

### <a name="billing-example---throughput-on-a-container-full-month"></a>Exempel på fakturering – dataflödet i en behållare (fullständig månad)

* Vi antar att du konfigurerar ett dataflöde på 1 000 RU/sek i en behållare och det finns i 24 timmar * 30 dagar månaden = 720 timmar totalt.  

* 1 000 RU/sek är 10 enheter med 100 RU/SEK per timme för varje timme behållarna finns (det vill säga 1 000/100 = 10). 

* Multiplicera 10 enheter per timme med kostnaden för $0.008 (per 100 RU/SEK per timme) = 0,08 USD per timme. 

* Multiplicera 0,08 USD per timme med antalet timmar i månaden är lika med $0.08 * 24 timmar * 30 dagar = $57.60 för månaden.  

* Den totala månadsfaktura 7 200 enheter (av 100 ru: er), som kommer att kosta $57.60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Exempel på fakturering – dataflödet i en behållare (del av en månad)

* Anta att vi skapa en behållare med etablerat dataflöde på 2 500 RU/sek. Behållaren finns i 24 timmar under månaden (exempelvis kan vi ta bort den 24 timmar när vi har skapat den).  

* Sedan ska vi se 600 enheter på fakturan (2 500 RU/SEK / 100 RU/sekund och enhet * 24 timmar). Kostnaden blir $4.80 (600 enheter * $0.008 per enhet).

* Total faktura för månaden blir $4.80.

### <a name="billing-rate-if-storage-size-changes"></a>Fakturering om lagringsstorlek ändras

Lagringskapacitet debiteras i enheter enligt den högsta datamängden i GB som lagras varje dag under en månad. Om du till exempel använder 100 GB lagringsutrymme under den första hälften av månaden och 50 GB resten av månaden debiteras du för 75 GB lagringsutrymme den månaden.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Debiteringstaxa när behållare eller en uppsättning behållare är aktiva i mindre än en timme

Du faktureras det fasta priset för varje timme behållare eller databasen existerar, oavsett användning eller om behållare eller databasen är aktiv i mindre än en timme. Om du skapar en behållare eller en databas och raderar den 5 minuter senare, kommer din faktura inkluderar en timme.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Fakturering när dataflödet i en behållare eller databasen kan skalas upp/ned

Om du ökar det etablerade dataflödet kl. 9:30 från 400 RU/s till 1000 RU/sek och sedan minskar det etablerade dataflödet kl. 10:45 tillbaka till 400 RU/sek, debiteras du för två timmar med 1000 RU/sek. 

Om du ökar det etablerade dataflödet för en behållare eller en uppsättning behållare till kl. 9:30 från 100 K RU/sek 200-K RU/sek och sedan lägre etablerade dataflödet kl. 10:45 tillbaka till 100 K RU/sek, debiteras du för två timmar med 200 K RU/sek.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Fakturering exempel: flera behållare med dedikerat dataflöde läge

* Om du skapar ett Azure Cosmos-konto i USA, östra 2 med två behållare med etablerat dataflöde på 500 RU/sek och 700 RU/sek respektive, skulle du ha ett totalt etablerat dataflöde på 1 200 RU/sek.  

* Du debiteras 1 200/100 * $0.008 = 0,096 USD / timme. 

* Om dataflödet behöver ändras och du har ökar varje behållares kapacitet med 500 RU/sek och samtidigt skapar en ny obegränsad behållare med 20 000 RU/SEK blir den totala etablerade kapaciteten blir 22,200 RU/sek (1 000 RU/SEK + 1 200 RU/sek + 20 000 ru/sek).  

* Din faktura då enligt: $0.008 x 222 = $1.776 / timme. 

* I en månad med 720 timmar (24 timmar * 30 dagar), om 500 timmar har etablerat dataflöde var 1200 RU/s och de återstående 220 timmarna etablerade dataflöde var 22,200 RU/sek visar din månatliga faktura: 500 x 0,096 USD / timme + 220 × $1.776 / timme = 438.72 $/ månad.

![Dedikerat dataflöde faktura-exempel](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Fakturering exempel: behållare med delade genomflödesläge

* Om du skapar ett Azure Cosmos-konto i USA, östra 2 med två Azure Cosmos-databaser (med en uppsättning behållare som delar dataflödet på databasnivå) med etablerat dataflöde på 50 K RU/sek och 70-K RU/sek respektive skulle har du totalt etablerad genomströmning på 120 K RU/sek.  

* Du debiteras 1200 x $0.008 = $9.60 / timme. 

* Om dataflödet behöver ändras och du ökar varje databas etablerat dataflöde med 10 K RU/sek för varje databas och du lägger till en ny behållare till första databasen med dedikerat dataflöde 15-K RU/sek till din delade dataflöde-databas, ditt totala etablerade kapaciteten är 155-K RU/sek (60 K RU/sek + 80 K RU/sek + 15 K RU/sek).  

* Din faktura då enligt: 1,550 * $0.008 = $12.40 / timme.  

* Under en månad med 720 timmar om 300 timmar etablerade dataflöde var 120 K RU/sek och för de återstående 420 timmarna etablerade dataflöde var 155-K RU/sek visar din månatliga faktura: 300 x $9.60 / timme + 420 x $12.40 / timme = $2,880 + $5,208 = 8,088 $/ månad. 

![Delade dataflöde faktura-exempel](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Faktureringsexempel med geo-replikering och multimaster  

Du kan lägga till/ta bort Azure-regioner överallt i världen till ditt Azure Cosmos DB-databaskonto när som helst. Det dataflöde som du har konfigurerat för olika Azure Cosmos DB-databaser och behållare kommer att reserveras i varje Azure-regioner som associeras med din Azure Cosmos-databaskonto. Om summan av dataflöde (RU/sek) har konfigurerats på alla databaser och behållare i din Azure Cosmos-databas-konto (etablerats per timme) är T och antal Azure-regioner som associeras med ditt databaskonto är N och det totala antalet dataflöde för en viss timme, för din Azure-Cosmos-databaskonto, (a) konfigurerats med en enda skrivregionen är lika med T x N RU/sek och (b) som konfigurerats med alla regioner som kan bearbeta skrivningar är lika med T x (N + 1) RU/sek , respektive. Dataflöde (enkel skrivregionen) kostar $0.008/ timme per 100 RU/sek och etablerat dataflöde med flera skrivbara regioner (flera huvudservrar config) kostar $0.016 / per timme per 100 RU/sek (se den [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/)). Om dess enda skriva region eller flera Skriv-regioner, kan Azure Cosmos DB du läsa data från alla regioner.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Fakturering exempel: skriver för flera regioner Azure Cosmos-konto, en enda region

Vi antar att du har en Azure Cosmos-behållare i västra USA. Behållaren har skapats med dataflödet 10 K RU/sek och du lagrar 1 TB data till den här månaden. Anta att du lägger till tre regioner (östra USA, Nordeuropa och Östasien) till Azure Cosmos-kontot, var och en med samma lagringsutrymme och dataflöde. Din totala månadsfaktura blir (förutsatt att 30 dagar i månaden). Fakturan blir som följer: 

|**Objekt** |**Användning (månad)** |**Pris** |**Månadskostnad** |
|---------|---------|---------|-------|
|Dataflödesfaktura för containrar i USA, västra      | 10 K RU/sek * 24 * 30    |$0.008 per 100 RU/SEK per timme   |$576|
|Dataflödesfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien       | 3 * 10K RU/sek * 24 * 30    |$0.008 per 100 RU/SEK per timme  |$ 1 728|
|Lagringsfaktura för containrar i USA, västra      | 250 GB    |$0.25/ GB  |$62.50|
|Lagringsfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien      | 3 * 250 GB    |$0.25/ GB  |$187.50|
|**Totalt**     |     |  |**$2,554**|

*Vi antar också att du 100 GB utgående data varje månad från behållaren i västra USA till östra USA, Nordeuropa och Östasien. Du faktureras för utgående trafik enligt för dataöverföring.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Fakturering exempel: skriver om Azure Cosmos-konto för flera regioner med flera regioner

Anta att du skapar en Azure Cosmos-behållare i västra USA. Behållaren har skapats med dataflödet 10 K RU/sek och du lagrar 1 TB data till den här månaden. Vi antar att du lägger till tre regioner (östra USA, Nordeuropa och Östasien), var och en med samma lagringsutrymme och dataflöde och du vill kunna skriva till behållarna i alla regioner som associeras med ditt Azure Cosmos-konto. Din totala månadsfaktura kommer vara (förutsatt att 30 dagar i månaden) på följande sätt:

|**Objekt** |**Användning (månad)**|**Pris** |**Månadskostnad** |
|---------|---------|---------|-------|
|Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)       | 10 K RU/sek * 24 * 30    |$0.016 per 100 RU/SEK per timme    |$1,152 |
|Dataflödesfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien (alla regioner är skrivbar)        | (3 + 1) * 10 K RU/sek * 24 * 30    |$0.016 per 100 RU/SEK per timme   |$4,608 |
|Lagringsfaktura för containrar i USA, västra      | 250 GB    |$0.25/ GB  |$62.50|
|Lagringsfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien      | 3 * 250 GB    |$0.25/ GB  |$187.50|
|**Totalt**     |     |  |**$6,010**|

*Vi antar också att du 100 GB utgående data varje månad från behållaren i västra USA till östra USA, Nordeuropa och Östasien. Du faktureras för utgående trafik enligt för dataöverföring.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Fakturering exempel: Azure Cosmos-konto med flera huvudservrar, databasnivå dataflöde, inklusive dedikerat dataflöde läge för vissa behållare

Anta att du i följande exempel, där vi har att ett Azure Cosmos-konto för flera regioner där alla regioner är skrivbar (flera huvudservrar config). För enkelhetens skull förutsätter vi lagringsstorlek förblir konstanta och de inte ändra och utelämnar det här exemplet att enklare. Det etablerade dataflödet under månaden olika enligt följande (förutsatt att 30 dagar eller 720 timmar): 

[0-100 timmar]:  

* Vi har skapat en tre region Azure Cosmos-konto (västra USA, östra USA, Norra Europa), där alla regioner är skrivbara 

* Vi har skapat en databas (D1) med delade dataflödet 10K RU/sek 

* Vi har skapat en databas (D2) med delade dataflöde 30-K RU/sek och  

* Vi har skapat en behållare (C1) med dedikerat dataflöde 20 K RU/sek 

[101-200 timmar]:  

* Skalas upp databasen (D1) till 50 K RU/sek 

* Skalas upp databasen (D2) till 70 K RU/sek  

* Vi har tagit bort behållaren (C1)  

[201-300 timmar]:  

* Vi skapade behållaren (C1) igen med dedikerat dataflöde 20 K RU/sek 

[301 400 timmar]:  

* Vi har tagit bort en av regionerna från Azure Cosmos-konto (# skrivbar regioner är nu 2) 

* Skalas ned databas (D1) så att 10 K RU/sek 

* Skalas upp databasen (D2) till 80 K RU/sek  

* Vi tagit bort behållaren (C1) igen 

[401-500 timmar]:  

* Skalas ned databas (D2) så att 10 K RU/sek  

* Vi skapade behållaren (C1) igen med dedikerat dataflöde 20 K RU/sek 

[501-700 timmar]:  

* Skalas upp databasen (D1) till 20 K RU/sek  

* Skalas upp databasen (D2) till 100 K RU/sek  

* Vi tagit bort behållaren (C1) igen  

[701 720 timmar]:  

* Skalas ned databas (D2) så att 50 K RU/sek  

Visuellt visas ändringarna i totalt etablerat dataflöde under 720 timmar för månaden på bilden nedan: 

![Riktiga exempel](./media/understand-your-bill/bill-example3.png)

Den totala månadsfaktura kommer (förutsatt att 30 dagar i 720 timmar under en månad) kommer att beräknas enligt följande:

|**Timmar**  |**RU/s** |**Objekt** |**Användning (per timme)** |**Kostnad** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1:-- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$ 1 920  |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5,760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>' C1: 20 K RU/SEK/100 * $0.016 * 100 timmar = $320 |$2,240 '  |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6,720 |
|[301 400] |D1:10K <br/>D2:80K <br/>C1:-- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$ 1 440   |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1,280  |
|[501-700] |D1:20K <br>D2:100K <br>C1:-- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3,840  |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$ 7 680  |
|[701 720] |D1:20K <br/>D2:50K <br/>C1:-- |Dataflödesfaktura för behållare i västra USA (alla regioner är skrivbar)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Dataflödesfaktura för 2 ytterligare regioner: Östra USA, Norra Europa (alla regioner är skrivbar)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Total månadskostnad**  | |**$38,688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Uppskatta proaktivt din månatliga faktura  

Anta att du har ett annat exempel är där du vill proaktivt beräkna din faktura innan den månaden. Du kan beräkna din faktura på följande sätt:

|**Kostnaden för lagring** | |
|----|----|
|Genomsnittlig post storlek (KB) |1 |
|Antal poster  |100 000 000  |
|Totalt lagringsutrymme (GB)  |100 |
|Månatlig kostnad per GB  |$0.25  |
|Förväntade månadskostnad för lagring   |$25,00  |

<br>

|**Kostnaden för dataflöde** | | | |
|----|----|----|----|
|Åtgärdstyp| Begäranden per sekund| Genomsn. RU/begäran| RU: er som behövs|
|Skriva| 100 | 5 | 500|
|Läsa| 400| 1| 400|

Totalt antal RU/sek: 500 + 400 = 900 timkostnad: 900/100 * $0.008 = $0.072 förväntat månadskostnaden för dataflöde (förutsatt att 31 dagar): $0.072 * 24 * 31 = $53.57

**Total månadskostnad**

Total månadskostnad = månatlig kostnad för lagring + månadskostnaden för dataflöde Total månadskostnad = $25,00 + $53.57 = $78.57

*Priser kan variera beroende på region. Uppdaterad prisinformation finns i den [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fakturering med Azure Cosmos DB reserverad kapacitet

Azure Cosmos DB reserverad kapacitet kan du köpa dataflöde i förväg (en reserverad kapacitet eller en reservation) som kan tillämpas på alla Azure Cosmos DB-databaser och behållare (för alla API: et eller data model) i alla Azure-regioner. Eftersom etablerat dataflöde priset varierar per region, hjälper det för att tänka på reserverad kapacitet som en kredit som du har köpt till ett rabatterat pris, som kan hämtas från för det etablerade dataflödet på respektive priset i varje region. Anta exempelvis att du har en Azure-Cosmos-konto med en enskild behållare etablerats med 50 K RU/sek och globalt replikerade två regioner – östra USA och östra Japan. Om du väljer alternativet betala per användning betalar du:  

* i USA, östra: för 50 K RU/sek taxan $0.008 per 100 RU/sek i den regionen 

* i östra Japan: för 50 K RU/sek taxan $0.009 per 100 RU/sek i den regionen

Din totala faktura (utan reserverad kapacitet) skulle vara (förutsatt att 30 dagar eller 720 timmar): 

|**Region**| **Pris per timme per 100 RU/s**|**Enheter (RU/s)**|**Fakturerat belopp (per timme)**| **Fakturerat belopp (per månad)**|
|----|----|----|----|----|
|Östra USA|$0.008 |50 K|$4|$2,880 |
|Östra Japan|$0.009 |50 K| $4,50 |$3,240 |
|Totalt|||$8,50|$6,120 |

Anta att du har köpt reserverad kapacitet i stället. Du kan köpa reserverad kapacitet för 100 K RU/sek till samma pris $56,064 i ett år (med 20% rabatt) eller $6.40 per timme. Se priser för reserverad kapacitet på den [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Kostnaden för dataflöde (betala per användning): 100 000 RU/SEK/100 * $0.008/timme * 8760 timmar under ett år = $70,080 

* Kostnaden för dataflöde (med reserverad kapacitet) $70,080 rabatt på 20% = $56,064 

Vad du har ett effektivt sätt köpt är en kredit på 8 USD per timme, för 100 K RU/sek med listpris i östra USA till samma pris $6.40 per timme. Sedan kan du rita från den här reservationen förbetald dataflöde per timme för etablerat dataflöde kapaciteten i alla globala Azure-regioner till respektive regionala lista priser för din prenumeration. Du kommer att kunna rita $8.00 värt av dataflöde per timme i det här exemplet där du etablerar 50 K RU/sek varje i östra USA och östra Japan, och kommer att faktureras för överförbrukning på 0,50 per timme (eller 360 $/ månad). 

|**Region**| **Pris per timme per 100 RU/s**|**Enheter (RU/s)**| **Fakturerat belopp (per timme)**| **Fakturerat belopp (per månad)**|
|----|----|----|----|----|
|Östra USA|$0.008 |50 K|$4|$2,880 |
|Östra Japan|$0.009 |50 K| $4,50 |$3,240 |
|||Användningsbaserad betalning|$8,50|$6120|
|Köpt reserverad kapacitet|$0.0064 (20% rabatt) |100 RU/sek eller 8 USD kapacitet förköpta |– 8 USD|-$5,760 |
|Nettofaktura|||0,50 |$360 |

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta till Lär dig mer om kostnadsoptimeringar i Azure Cosmos DB i följande artiklar:

* Läs mer om [hur Cosmos DB prismodellen är ett kostnadseffektivt alternativ för kunder](total-cost-ownership.md)
* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för Azure Cosmos-konton för flera regioner](optimize-cost-regions.md)
