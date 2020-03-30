---
title: Användningsfall för inbyggd analys med Azure Cosmos DB.
description: Lär dig hur du använder inbyggda analyser med Azure Cosmos DB i olika användningsfall.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72757067"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Användningsfall för inbyggd analys med Azure Cosmos DB

Följande användningsfall kan uppnås med hjälp av den inbyggda analysen med Apache Spark i Azure Cosmos DB:

## <a name="htap-scenarios"></a>HTAP-scenarier

Inbyggd analys i Azure Cosmos DB kan användas för att:

* Identifiera bedrägerier under transaktionsbearbetning.
* Ge rekommendationer till shoppare när de surfar på en E-handel butik.
* Varna operatörerna för det förestående felet hos en kritisk utrustning.
* Skapa snabb och användbar insikt genom att bädda in analyser i realtid direkt på driftdata.

Azure Cosmos DB supports Hybrid Transactional/Analytical Processing (HTAP) scenarios by using the natively built-in Apache Spark. Azure Cosmos DB tar bort den operativa och analytiska separation som följer med de traditionella systemen.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globalt distribuerad datasjö utan att kräva ETL

Med inbyggt inbyggt Apache Spark erbjuder Azure Cosmos DB ett snabbt, enkelt och skalbart sätt att skapa en globalt distribuerad datasjö som tillhandahåller en enda systemavbildning. Den globalt distribuerade flermodellsdata eliminerar behovet av att investera i dyra ETL-pipelines och skalor på begäran, vilket revolutionerar hur datateam analyserar sina datauppsättningar.

## <a name="time-series-analytics-over-historic-data"></a>Tidsserieanalys över historiska data

I vissa fall kan du behöva svara på frågor baserat på data som vid en viss tidpunkt över händelser som slutförts tidigare. Till exempel, för att få räkningen av CRM-aktivitetsstatus vid ett visst datum. Om du körde rapporten för en vecka sedan skulle antalet statusar vara enligt statusarna för varje aktivitet vid den tidpunkten. Köra samma rapport i dag kommer att ge dig räkna av de aktiviteter vars status är som de är i dag, som kan ha förändrats sedan förra veckan, eftersom de går igenom sin livscykel från öppen till nära. Så måste du rapportera om ögonblicksbilden i varje skede av ärendets livscykel.

I traditionella scenarier för informationslager är begreppet ögonblicksbild inte möjligt eftersom informationslagermen inte är utformade för att införliva den och data bara ger en aktuell vy över vad som händer. Med Azure Cosmos DB har användarna möjlighet att implementera begreppet tidsresor, att kunna fråga och köra analyser på data retroaktivt och be om hur data tittade på en viss tidpunkt i historiken. Det innebär att användarna enkelt kan visa både aktuella och historiska vyer av data och köra analyser på den.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globalt distribuerad maskininlärning och AI

Som företag brottas med snabbt växande volymer av data och en växande mängd olika datatyper och format, förmågan att få djupare och mer exakta insikter blir nästan omöjligt på petabyte skala över hela världen. Med inbyggd Apache Spark tillhandahåller Azure Cosmos DB en globalt distribuerad analysplattform som erbjuder omfattande bibliotek med maskininlärningsalgoritmer. Du kan använda interaktiva Jupyter-anteckningsböcker för att skapa och träna modeller och klusterhanteringsfunktioner. Med dessa funktioner kan du etablera högjusterade och automatiskt elastiska Spark-kluster på begäran.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning på data med flera modeller globalt distribuerade

Djupinlärning är det perfekta sättet att tillhandahålla lösningar för stordatas prediktiva analyser när datavolymen och komplexiteten fortsätter att växa. Med djupinlärning kan företag utnyttja kraften i ostrukturerade och halvstrukturerade data för att leverera användningsfall som utnyttjar tekniker som AI, bearbetning av naturligt språk med mera.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Rapportering (integrering med Power Apps, Power BI)

Power BI tillhandahåller interaktiva visualiseringar med business intelligence-funktioner med självbetjäning, vilket gör det möjligt för slutanvändare att skapa rapporter och instrumentpaneler på egen hand. Med den inbyggda Spark-anslutningen kan du ansluta Power BI Desktop till Apache Spark-kluster i Azure Cosmos DB. Med den här kopplingen kan du använda direktfrågan för att avlasta bearbetning till Apache Spark i Azure Cosmos DB, vilket är bra när du har en enorm mängd data som du inte vill läsa in i Power BI eller när du vill utföra analys i nära realtid.

## <a name="iot-analytics-at-global-scale"></a>IoT-analys på global nivå

De data som genereras från ökande nätverkssensorer ger en oöverträffad insyn i tidigare ogenomskinliga system och processer. Nyckeln är att hitta användbara insikter i denna torrent av information oavsett var IoT-enheter distribueras över hela världen. Azure Cosmos DB gör det möjligt för IOT-företag att analysera höghastighetssensor och tidsseriedata i realtid var som helst i världen. Det gör att du kan utnyttja det verkliga värdet av en sammankopplad värld för att leverera förbättrade kundupplevelser, operativa effektivitetsvinster och nya intäktsmöjligheter.

## <a name="stream-processing-and-event-analytics"></a>Strömma bearbetning och händelseanalys 

Från loggfiler till sensordata, företag har alltmer behov av att hantera "strömmar" av data. Dessa data kommer i en stadig ström, ofta från flera källor samtidigt. Även om det är möjligt att lagra dessa dataströmmar på disk och analysera dem retroaktivt, kan det ibland vara förnuftigt eller viktigt att bearbeta och agera på data när den anländer. Dataströmmar som rör finansiella transaktioner kan till exempel bearbetas i realtid för att identifiera och vägra potentiellt bedrägliga transaktioner.

## <a name="interactive-analytics"></a>Interaktiva analyser

Förutom att köra fördefinierade frågor för att skapa statiska instrumentpaneler för försäljning, produktivitet eller aktiekurser, kanske du vill utforska data interaktivt. Interaktiv analys kan du ställa frågor, visa resultaten, ändra den första frågan baserat på svaret eller borra djupare i resultat. Apache Spark i Azure Cosmos DB stöder interaktiva frågor genom att svara och anpassa sig snabbt.

## <a name="data-exploration-using-jupyter-notebooks"></a>Datautforskning med jupyter-anteckningsböcker

När du har en ny datauppsättning, innan du dyker in i löpmodeller och tester, måste du granska dina data. Med andra ord måste du utföra utforskande dataanalys. Datautforskning kan ligga till grund för flera beslut. Du kan till exempel hitta information som de metoder som är lämpliga att använda på dina data, om data uppfyller vissa modelleringsantaganden, om data ska rengöras, omstruktureras etc. Genom att använda Azure Cosmos DB:s inbyggda Jupyter-anteckningsböcker och Apache Spark kan du göra snabb och effektiv dataanalys av transaktionsdata och analysdata.

## <a name="next-steps"></a>Nästa steg

För att komma igång med dessa användningsfall på gå till följande artiklar:

* [Inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Aktivera anteckningsböcker för Azure Cosmos-konton](enable-notebooks.md)
* [Skapa en anteckningsbok för att analysera och visualisera data](create-notebook-visualize-data.md)