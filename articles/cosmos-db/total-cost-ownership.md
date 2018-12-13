---
title: Totalkostnaden för Ownership(TCO) med Azure Cosmos DB
description: Den här artikeln jämför totalkostnad för ägarskap av Azure Cosmos DB med IaaS- och lokala databaser
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: a15a704cfba9260daac0cd5edfdf8b4566348628
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263967"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Totalkostnaden för Ownership(TCO) med Azure Cosmos DB

Azure Cosmos DB har utformats med de detaljerade styrning för flera innehavare och resursen. Den här designen kan Azure Cosmos DB i betydligt lägre kostnad och hjälp användare spara. Azure Cosmos DB stöder för närvarande mer än 280 slutkundsarbetsbelastningar på en dator med densitet ökar kontinuerligt och tusentals kundens arbetsbelastningar inom ett kluster. Den belastningsutjämnar repliker av kunder arbetsbelastningar på olika datorer i ett kluster och över flera kluster i ett datacenter du mer information finns i [Azure Cosmos DB: Skicka globalt distribuerade databaser gräns](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). På grund av resurs-styrning, flera innehavare och inbyggd integration med resten av Azure-infrastrukturen kan är Azure Cosmos DB i genomsnitt 4 till 6 gånger billigare än MongoDB, Cassandra eller andra OSS NoSQL som körs på IaaS och upp till 10 gånger billigare än databasen motorer som körs lokalt. Se dokumentet på [den totala ägandekostnaden (icke) i en molntjänst för NoSQL-databas](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

OSS NoSQL database-lösningar, till exempel Apache Cassandra, MongoDB, HBase, motorer utformades för on-premises. De är likvärdiga med en Resource Manager-mall med en klientdatabas för att hantera de etablerade kluster och övervakningsstöd när erbjuds som en hanterad tjänst. Arkitekturer för OSS NoSQL kräver betydande driftsomkostnader och expertis kan vara svårt och dyrt att hitta. Å andra sidan, är Azure Cosmos DB en fullständigt hanterad molntjänst som gör att utvecklare kan fokusera på företagets innovationer i stället för hantering och underhåll av databasinfrastruktur. 

Till skillnad från molnbaserade database-tjänsten Azure Cosmos DB har OSS NoSQL databasmotorer utformades och byggdes med resursstyrning eller detaljerade flera innehavare som grundläggande arkitekturprinciper inte. OSS NoSQL databasmotorer som Cassandra och MongoDB gör en grundläggande antagandet att alla resurser på den virtuella datorn som de körs är tillgängliga för deras användning. Många av dessa databasmotorer fungerar inte om mängden resurser faller under ett visst tröskelvärde. Till exempel för liten virtuell dator instanser och de är tillgängliga med leverantören rekommenderade konfigurationer föreslå vanligtvis storskaliga virtuella datorer med högre kostnad. Det är därför inte möjligt att vara värd för en OSS NoSQL eller andra lokala databas-motorn och göra den tillgänglig med hjälp av en förbrukningsbaserad laddas modell som begäranden per sekund eller förbrukad lagring.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Totalkostnad för ägarskap av Azure Cosmos DB 

Den serverfria etableringsmodellen hos Azure Cosmos DB eliminerar behovet av att etablera databasinfrastrukturen. Azure Cosmos DB-resurser har angetts utan något behov för särskilda konfigurationer eller licensiering. Därför kan de Azure Cosmos DB-baserade program kan köras med upp till 70 procent totalkostnad för ägarskap besparingar jämfört med OSS NoSQL-databaser. Vissa i realtid exempel finns i [kunden användningsfall](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Andra fördelar med Azure Cosmos DB-prismodellen är:

* **Bra värde för pris:** Marknaden analytiker, kunder och partner har bekräftat ett större värde för alla funktioner som Azure Cosmos DB erbjuder till ett mycket lägre pris jämfört med vad kunder får när du implementerar dessa lösningar på egen hand eller via andra leverantörer. Databasen har sådana global distribution, flera huvudservrar, väldefinierade och intuitiv konsekvensmodeller, automatisk indexering är avsevärt förenklad med Azure Cosmos DB utan komplexitet, arbete, eller driftstopp.

* **Det krävs ingen NoSQL devops-administration:** En behöver inte använda devops utföra underhåll, skala eller patch om du vill hantera distributioner med Azure Cosmos DB. Du kan köra de alla arbetsbelastningar som du gör med OSS NoSQL kluster som finns lokalt eller i molninfrastruktur.

![Azure Cosmos DB i ägandekostnad](./media/total-cost-ownership/tco.png)

* **Möjligheten att Elastiskt skala:** Dataflöde för Azure Cosmos DB kan skalas upp och ned, så att du kan minska ägandekostnaden under tider med låg belastning. OSS NoSQL-kluster som distribueras i molninfrastruktur erbjuder begränsad elasticitet och lokala distributioner är inte elastiska per definition. I Azure Cosmos DB om du etablerar mer dataflöde är ditt dataflöde säkert att skalas linjärt. Garantin säkerhetskopieras av finansiella serviceavtal och 99: e percentilen i valfri skala.

* **Stordriftsfördelar:** En hanterad tjänst som Azure Cosmos DB fungerar med ett stort antal noderna, integrerad internt med nätverk, lagring och beräkningar. På grund av Azure Cosmos DB storskalig, standardisering som du kan spara pengar.

* **Optimerad för molnet:** Azure Cosmos DB har utformats från grunden med detaljerade isolering för flera innehavare och prestanda. Det möjliggör optimalt placera, köra och belastningsutjämning tusentals olika klienter och sina arbetsbelastningar över kluster och datacenter. Däremot fungera den nuvarande generationen OSS NoSQL-databaser på plats med hela den virtuella datorn antas för att köra en enda klient arbetsbelastningen. Dessa databaser är också inte utformade för att använda en molnleverantör infrastruktur och maskinvara som helt. Till exempel en OSS NoSQL databasmotor är inte medvetna om skillnaderna mellan en virtuell dator som ned Vs en rutinmässig avbildning uppgradera eller faktumet att premium-disken är redan trevägs replikeras. Den kan inte dra nytta av dessa förmåner och skicka om fördelar och besparingar för kunder.

* **Du betalar per timme:** För storskaliga arbetsbelastningar som behöver skala vid någon tidpunkt endast debiteras per timme. Arbetsbelastningar på ett program som vanligtvis varierar mellan olika tidpunkter på året och data som det efterfrågas. Med Azure Cosmos DB kan skala du upp eller ned när du behöver och betala bara för vad du behöver. Med en lokal eller IaaS-värd system, kan inte du matcha den här modellen eftersom det inte är ett sätt att inaktivera maskinvaran varje timme. I sådana fall kan spara du eventuellt mellan 10 till 14 gånger på ett genomsnitt med Azure Cosmos DB.

* **Du får ett stort antal funktioner kostnadsfritt:** Skriva arbetsbelastningar med avsevärt billigare alternativ databastjänster i Azure Cosmos DB. Dessutom Azure Cosmos DB erbjuder funktioner som till exempel [automatisk indexering](indexing-policies.md), [Time to Live (TTL)](time-to-live.md), [Change Feed](change-feed.md) och andra utan ytterligare avgifter, något som oftast debiterar andra databastjänster.

* **Använder ett enhetligt valuta för olika arbetsbelastningar:** Till skillnad från andra erbjudanden i Azure Cosmos DB kan behöver du inte segment arbetsbelastningar, till exempel i läsningar och skrivningar. Eller etablera dataflöde på en per Arbetsbelastningstyp av som läses genomflöde jämfört med genomströmning för skrivning. I Azure Cosmos DB, etablerat dataflöde reserveras med hjälp av en enhetlig och normaliserade valuta när det gäller programbegäran eller RU/sek. Azure Cosmos DB göra inte så att tilldela prioritet till dina arbetsbelastningar, utför kapacitetsplanering eller betala för varje typ av kapacitet du separat. Sådana metoden kan du enkelt interchange samma RU/s mellan olika åtgärder och typer av arbetsbelastningar.

* **Du behöver etablera virtuella datorer att skala:** De flesta driftdatabaser kräver att du går med stora virtuella datorer för att undvika störningar grannar och Lös resursstyrning, om du vill skala. Detta ger belastningen och förskott kostar på kunderna. Du kan börja i liten skala och utöka storlekarna för arbetsbelastningen i stor skala utan problem, och utan driftavbrott eller inverkan på tillgänglighet för data med Azure Cosmos DB.

* **Du kan använda etablerade dataflöde för att en Maxgräns:** Miljöpåverkan av underordnade core multiplexering i Azure Cosmos DB kan du fylla det etablerade dataflödet i större utsträckning än IaaS finns alternativ eller erbjudanden från tredje part. Den här metoden sparar mycket mer än den alternativa lösningen.

* **Djupgående integration av Azure Cosmos DB med andra Azure-tjänster.** Azure Cosmos DB har en inbyggd integration med nätverk, beräkning, Azure Functions (utan Server), Azure IoT och andra Azure-tjänster. Med den här integreringen får du bästa möjliga prestanda, replikering av data över hela världen med robust garantier hastighet. Tredjeparts-lösningar kommer inte att kunna matcha eller skulle vanligtvis att debitera en avgift för att erbjuda funktioner.

* **Du får hög tillgänglighet, med minst 10-20-feldomäner som standard automatiskt:** Azure Cosmos DB stöder distribution av arbetsbelastningar över feldomäner, en funktion som är viktiga för hög tillgänglighet. Den erbjuder 99.999 hög tillgänglighet för läsning och skrivning vid 99: e percentilen över var som helst i världen. Kostnaden för att genomföra ungefär så här på egen hand eller via en lösning från tredje part, är hög.

* **Du får automatiskt alla enterprise-funktioner utan extra kostnad.** Azure Cosmos DB erbjuder den mest omfattande uppsättningen efterlevnadscertifiering, säkerhet och kryptering i vila och under rörelse utan extra kostnad (jämfört med våra konkurrenter). Du får automatiskt regional tillgänglighet var som helst i världen. Du kan omfatta din databas i valfritt antal Azure-regioner och lägga till eller ta bort regioner när som helst.

* **Du kan spara upp till 65% av kostnader med reserverad kapacitet:** Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig spara pengar genom att betala förväg för Azure Cosmos DB-resurserna för antingen ett eller tre år. Du kan avsevärt minska dina kostnader med ett eller tre år initiala åtaganden och spara mellan 20-65% rabatt jämfört med vanliga priser. Du kan få bättre serviceavtal när det gäller etablering av kapacitet på dina verksamhetskritiska arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur Azure Cosmos DB prismodellen är ett kostnadseffektivt alternativ för kunder](total-cost-ownership.md)
* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för flera regioner Cosmos-konton](optimize-cost-regions.md)
* Läs mer om [den totala ägandekostnaden (icke) i en molntjänst för NoSQL-databas](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
