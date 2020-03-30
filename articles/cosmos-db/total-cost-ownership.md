---
title: Total ägandekostnad (TCO) med Azure Cosmos DB
description: I den här artikeln jämförs den totala ägandekostnaden för Azure Cosmos DB med IaaS och lokala databaser
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754797"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Total ägandekostnad (TCO) med Azure Cosmos DB

Azure Cosmos DB har en detaljerad styrning av flera klientorganisationer och resurser. Den här designen gör att du kan använda Azure Cosmos DB till en betydligt lägre kostnad och spara pengar. Azure Cosmos DB har för närvarande stöd för fler än 280 kundarbetsbelastningar på en enskild dator och densiteten ökar hela tiden, och du kan köra tusentals kundarbetsbelastningar i ett kluster. Repliker av kundernas arbetsbelastningar belastningsutjämnas mellan olika datorer i ett kluster och mellan olika kluster i ett datacenter. Mer information finns i [Azure Cosmos DB: Pushing gränsen för globalt distribuerade databaser](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). På grund av resursstyrning, multi-arrende och inbyggd integrering med resten av Azure-infrastrukturen är Azure Cosmos DB i genomsnitt 4 till 6 gånger billigare än MongoDB, Cassandra eller andra OSS NoSQL som körs på IaaS och upp till 10 gånger billigare än databasen motorer som körs på plats. Se dokumentet om [Den totala kostnaden för (icke) ägarskap för en NoSQL-databasmolntjänst](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

OSS NoSQL-databaslösningar, såsom Apache Cassandra, MongoDB, HBase, motorer har utformats för lokala. När de erbjuds som en hanterad tjänst motsvarar de en Resource Manager-mall med en klientdatabas för hantering av de etablerade klustren och övervakningsstödet. OSS NoSQL-arkitekturer kräver betydande driftkostnader, och expertisen kan vara svår och dyr att hitta. Å andra sidan är Azure Cosmos DB en fullständigt hanterad molntjänst, som gör det möjligt för utvecklare att fokusera på affärsinnovation snarare än på att hantera och underhålla databasinfrastruktur. 

Till skillnad från en molnbaserad databastjänst Azure Cosmos DB har OSS NoSQL-databasmotorer inte utformats och byggts med resursstyrning eller finkornig flerarrätt som grundläggande arkitektoniska principer. OSS NoSQL databasmotorer som Cassandra och MongoDB göra ett grundläggande antagande att alla resurser i den virtuella maskinen som de körs är tillgängliga för deras användning. Många av dessa databasmotorer kan inte fungera om mängden resurser sjunker under ett visst tröskelvärde. Till exempel för små VM-instanser, och de är tillgängliga med leverantörsbe rekommenderad konfigurationer som föreslår vanligtvis storskaliga virtuella datorer med högre kostnad. Så det är inte möjligt att vara värd för en OSS NoSQL eller någon annan lokal databasmotor och göra den tillgänglig med hjälp av en förbrukningsbaserad laddningsmodell som begäranden per sekund eller förbrukad lagring.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Total ägandekostnad för Azure Cosmos DB 

Den serverlösa etableringsmodellen för Azure Cosmos DB eliminerar behovet av att överetablerar databasinfrastrukturen. Azure Cosmos DB-resurser tillhandahålls utan behov av specialiserade konfigurationer eller licensiering. Därför kan Azure Cosmos DB-stödda program köras med så mycket som 70 procent total ägandekostnad jämfört med OSS NoSQL-databaser. För några exempel i realtid finns i [kundanvändningsfall](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Andra fördelar med Azure Cosmos DB-prismodellen är:

* **Stort värde för priset:** Marknadsanalytiker, kunder och partner har bekräftat ett större värde för alla funktioner som Azure Cosmos DB erbjuder till ett mycket lägre pris jämfört med vad kunderna kan få när de implementerar dessa lösningar på egen hand eller via andra leverantörer. Databasen har sådana globala distributioner, multi-master, väldefinierade och intuitiva konsekvensmodeller, automatisk indexering förenklas avsevärt med Azure Cosmos DB utan komplexitet, omkostnader eller driftstopp.

* **Ingen NoSQL DevOps-administration krävs:** Med Azure Cosmos DB behöver man inte använda DevOps för att hantera distributioner, utföra underhåll, skala eller korrigera. Du kan köra alla arbetsbelastningar som du skulle göra med OSS NoSQL-kluster som finns lokalt eller i molninfrastruktur.

![Azure Cosmos DB ägandekostnad](./media/total-cost-ownership/tco.png)

* **Förmåga att elastiskt skala:** Azure Cosmos DB-dataflöde kan skalas upp och ned, så att du kan minska ägandekostnaden under icke-rusningstid. OSS NoSQL-kluster som distribueras på molninfrastruktur erbjuder begränsad elasticitet och lokala distributioner är inte elastiska per definition. I Azure Cosmos DB, om du etablerar mer dataflöde, är ditt dataflöde garanterat att skala linjärt. Denna garanti backas upp av finansiella SLA och på den 99: e percentilen i någon skala.

* **Stordriftsfördelar:** En hanterad tjänst som Azure Cosmos DB fungerar med ett stort antal noder, integrerade internt med nätverk, lagring och beräkningar. På grund av Azure Cosmos DB:s storskaliga standardisering kan du spara kostnader.

* **Optimerad för molnet:** Azure Cosmos DB är utformad från grunden med finkornig flerarrering och prestandaisolering. Detta gör det möjligt att optimalt placera, köra och balansera tusentals klienter och deras arbetsbelastningar över kluster och datacenter. Däremot fungerar den aktuella generationen av OSS NoSQL-databaser lokalt med hela den virtuella datorn som antas köra en enda klients arbetsbelastning. Dessa databaser är inte heller utformade för att utnyttja en molnleverantörs infrastruktur och maskinvara i full utsträckning. En OSS NoSQL-databasmotor är till exempel inte medveten om skillnaderna mellan att en virtuell dator är nere i Vs a routine image upgrade eller det faktum att premiumdisken redan är trevägsreplikerat. Det kan inte dra nytta av dessa fördelar och vidarebefordra fördelar och besparingar till kunder.

* **Du betalar per timme:** För storskaliga arbetsbelastningar, som behöver skalas när som helst, debiteras du bara per timme. Arbetsbelastningarna i ett program varierar vanligtvis mellan tider på året och av de data som efterfrågas. Med Azure Cosmos DB kan du skala upp eller ned som du behöver och bara betala för det du behöver. Med lokala eller IaaS-värdbaserade system kan du inte matcha den här modellen, eftersom det inte finns något sätt att inaktivera maskinvaran varje timme. I sådana fall kan du eventuellt spara mellan 10 och 14 gånger i genomsnitt med Azure Cosmos DB.

* **Du får många funktioner gratis:** I Azure Cosmos DB är skrivarbetsbelastningar betydligt billigare jämfört med alternativa databastjänster. Dessutom erbjuder Azure Cosmos DB funktioner som [automatisk indexering](indexing-policies.md), [Time to Live (TTL),](time-to-live.md) [Ändra feed](change-feed.md) och andra utan några extra avgifter, något som andra databastjänster vanligtvis tar ut.

* **Använder enhetlig valuta för olika arbetsbelastningar:** Till skillnad från alternativa erbjudanden, i Azure Cosmos DB, behöver du inte segmentera arbetsbelastningar, till exempel i läsningar och skrivningar. Eller etablera dataflöde på en typ per arbetsbelastning som läsdataflöde kontra skrivdataflöde. I Azure Cosmos DB reserveras etablerat dataflöde med hjälp av en enhetlig och normaliserad valuta när det gäller begärandeenheter eller RU/sek. Azure Cosmos DB tvingar dig inte att tilldela prioritet till dina arbetsbelastningar, utföra kapacitetsplanering eller betala för varje typ av kapacitet separat. Med ett sådant tillvägagångssätt kan du enkelt växla ut samma RU/s mellan olika operationer och arbetsbelastningstyper.

* **Kräver inte att etablering av virtuella datorer skalas:** De flesta operativa databaser kräver att du går med stora virtuella datorer för att undvika bullriga grannar och för lös resursstyrning, om du vill skala. Detta lägger bördan och det initiala åtagandet av kostnaden på kunderna. Med Azure Cosmos DB kan du börja i liten skala och växa till de storskaliga arbetsbelastningsstorlekarna sömlöst och utan avbrott eller påverkan på datatillgängligheten.

* **Du kan använda etablerat dataflöde till en maximal gräns:** På grund av sub-core multiplexing i Azure Cosmos DB kan du mätta det etablerade dataflödet i större utsträckning än IaaS-värdbaserade alternativ eller erbjudanden från tredje part. Den här metoden sparar mycket mer än de alternativa lösningarna.

* **Djup integrering av Azure Cosmos DB med andra Azure-tjänster.** Azure Cosmos DB har en inbyggd integrering med nätverk, beräkning, Azure-funktioner (serverlösa), Azure IoT och andra Azure-tjänster. Med den här integrationen får du bästa prestanda, hastighet för datareplikering över hela världen med robusta garantier. Tredjepartslösningarna kan inte matcha eller tar normalt ut en premie för att erbjuda sådana funktioner.

* **Du får automatiskt hög tillgänglighet, med minst 10-20 feldomäner som standard:** Azure Cosmos DB stöder distribution av arbetsbelastningar mellan feldomäner, en funktion som är avgörande för hög tillgänglighet. Det erbjuder 99.999 hög tillgänglighet för läsningar och skriver på den 99: e percentilen över var som helst i världen. Kostnaden för att genomföra något sådant på egen hand eller genom en tredjepartslösning, skulle vara hög.

* **Du får automatiskt alla företagsfunktioner, utan extra kostnad.** Azure Cosmos DB erbjuder den mest omfattande uppsättningen efterlevnadscertifieringar, säkerhet och kryptering i vila och i rörelse utan extra kostnad (jämfört med våra konkurrenter). Du får automatiskt regional tillgänglighet var som helst i världen. Du kan sträcka dig över databasen över valfritt antal Azure-regioner och lägga till eller ta bort regioner när som helst.

* **Du kan spara upp till 65 % av kostnaderna med reserverad kapacitet:** Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig att spara pengar genom att betala för Azure Cosmos DB-resurser för antingen ett år eller tre år. Du kan avsevärt minska dina kostnader med ett år eller tre år i förskott åtaganden och spara mellan 20-65% rabatter jämfört med den ordinarie prissättningen. På dina verksamhetskritiska arbetsbelastningar kan du få bättre SLA när det gäller etableringskapacitet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur Azure Cosmos DB-prismodell är kostnadseffektiv för kunderna](total-cost-ownership.md)
* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Läs mer om [den totala kostnaden för (icke) ägande av en NoSQL Database Cloud Service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
