---
title: Använd-fall för inbyggd analys med Azure Cosmos DB.
description: Lär dig hur du använder inbyggd analys med Azure Cosmos DB i olika användnings fall.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 04/20/2020
ms.reviewer: sngun
ms.openlocfilehash: 9b1c3435222ada52c01f21c2c242dc886f566a81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192858"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Användnings fall för inbyggd analys med Azure Cosmos DB

Följande användnings fall kan uppnås med hjälp av den inbyggda analysen med Apache Spark i Azure Cosmos DB:

## <a name="htap-scenarios"></a>HTAP-scenarier

Inbyggd analys i Azure Cosmos DB kan användas för att:

* Identifiera bedrägerier under transaktions bearbetning.
* Ge rekommendationer till shoppare när de söker i ett ECommerce-lager.
* Aviserings operatörer till det kommande problemet med en kritisk utrustning för tillverkning.
* Skapa snabba, användbara insikter genom att bädda in real tids analys direkt på drifts data.

Azure Cosmos DB stöder hybrid scenarier för transaktions-/analys bearbetning (HTAP) med hjälp av det inbyggda Apache Spark. Azure Cosmos DB tar bort den operativa och analytiska separationen som medföljer de traditionella systemen.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globalt distribuerad data Lake utan att kräva någon ETL

Med inbyggd Apache Spark är Azure Cosmos DB ett snabbt, enkelt och skalbart sätt att bygga ett globalt distribuerat data Lake som tillhandahåller en enda system avbildning. De globalt distribuerade multi-Model-data eliminerar behovet av att investera i dyra ETL-pipelines och skalas på begäran, vilket gör att data teamen kan analysera data uppsättningarna.

## <a name="time-series-analytics-over-historic-data"></a>Tids serie analys över historiska data

I vissa fall kan du behöva svara på frågor baserat på data som vid en viss tidpunkt över händelser som har slutförts tidigare. Till exempel för att få antalet status för CRM-aktiviteter vid ett visst datum. Om du körde rapporten en vecka sedan, skulle antalet statusar vara lika med status för varje aktivitet vid den tidpunkten. Om du kör samma rapport idag får du antalet aktiviteter vars status är i dag, som kan ha ändrats sedan förra veckan, när de går igenom deras livs cykel från öppna till Stäng. Så du måste rapportera om ögonblicks bilden i varje steg i väskans livs cykel.

Med Azure Cosmos DB har användarna möjlighet att implementera Tidsresans tids resa, kunna fråga och köra analyser på data i efterhand och fråga efter hur data som visas vid en viss tidpunkt i historiken. Det innebär att användare enkelt kan se både aktuella och historiska vyer av data och köra analyser på den.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globalt distribuerad maskin inlärning och AI

Eftersom företag tävla med snabbt växande data volymer och en rad olika data typer och format kan möjligheten att få djupare och mer exakta insikter bli nära omöjlig vid petabyte-skala över hela världen. Med inbyggd Apache Spark är Azure Cosmos DB en globalt distribuerad analys plattform som erbjuder ett omfattande bibliotek med Machine Learning-algoritmer. Du kan använda interaktiva Jupyter-anteckningsböcker för att skapa och träna modeller och funktioner för kluster hantering. Med dessa funktioner kan du etablera högjusterade och automatiskt elastiska Spark-kluster på begäran.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Djup inlärning för globalt distribuerade data i flera modeller

Djup inlärning är det perfekta sättet att tillhandahålla Big data förutsägelse analys lösningar som data volym och komplexitet fortsätter att växa. Med djup inlärning kan företag dra fördel av ostrukturerade och delvis strukturerade data för att leverera användnings fall som utnyttjar metoder som AI, naturlig språk bearbetning med mera.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Rapportering (integrering med Power Apps, Power BI)

Power BI tillhandahåller interaktiva visualiseringar med självbetjänings Business Intelligence funktioner som gör det möjligt för slutanvändare att skapa rapporter och instrument paneler på egen hand. Genom att använda den inbyggda Spark-anslutningen kan du ansluta Power BI Desktop till Apache Spark kluster i Azure Cosmos DB. Med den här anslutnings tjänsten kan du använda direkt fråga för att avlasta bearbetningen till Apache Spark i Azure Cosmos DB, vilket är bra om du har en enorm mängd data som du inte vill läsa in i Power BI eller när du vill utföra en nära real tids analys.

## <a name="iot-analytics-at-global-scale"></a>IoT Analytics i global skala

Data som genereras från ökande nätverks sensorer ger en oöverträffad insyn i tidigare täckande system och processer. Nyckeln är att hitta åtgärds bara insikter i denna torrent, oavsett var IoT-enheter är distribuerade världen över. Med Azure Cosmos DB kan IOT-företag analysera hög Velocity-sensor och Time Series-data i real tid över hela världen. Med den kan du dra nytta av det sanna värdet för en sammankopplad värld för att leverera förbättrade kund upplevelser, drift effektivitet och nya intäkts möjligheter.

## <a name="stream-processing-and-event-analytics"></a>Stream-bearbetning och händelse analys 

Från loggfiler till sensor data kan företag i allt större utsträckning behöva hantera data strömmar av data. Dessa data anländer i en stabil ström, ofta från flera källor samtidigt. Även om det är möjligt att lagra dessa data strömmar på disk och analysera dem retroaktivt, kan det ibland vara lämpliga eller viktigt att bearbeta och agera på data när de tas emot. Till exempel kan strömmar av data som är relaterade till ekonomiska transaktioner bearbetas i real tid för att identifiera och neka potentiellt bedrägliga transaktioner.

## <a name="interactive-analytics"></a>Interaktiv analys

Förutom att köra fördefinierade frågor för att skapa statiska instrument paneler för försäljnings-, produktivitets-eller aktie priser kanske du vill utforska data interaktivt. Med interaktiva analyser kan du ställa frågor, se resultatet, ändra den ursprungliga frågan baserat på svaret eller öka detalj nivån i resultatet. Apache Spark i Azure Cosmos DB stöder interaktiva frågor genom att svara och anpassas snabbt.

## <a name="data-exploration-using-jupyter-notebooks"></a>Data utforskning med Jupyter-anteckningsböcker

När du har en ny data uppsättning måste du kontrol lera dina data innan du börjar köra modeller och tester. Med andra ord måste du utföra analys av analys av data. Data utforskning kan informera flera beslut. Du kan till exempel hitta information som de metoder som är lämpliga att använda på dina data, om data uppfyller vissa modellerings antaganden, om data ska rensas, omstruktureras osv. Genom att använda Azure Cosmos DB inbyggda bärbara Jupyter-anteckningsböcker och Apache Spark kan du snabbt och effektivt analysera data analyser på transaktions-och analys data.

## <a name="next-steps"></a>Nästa steg

För att komma igång med dessa användnings fall på går du till följande artiklar:

* [Inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Aktivera antecknings böcker för Azure Cosmos-konton](enable-notebooks.md)
* [Skapa en bärbar dator för att analysera och visualisera data](create-notebook-visualize-data.md)