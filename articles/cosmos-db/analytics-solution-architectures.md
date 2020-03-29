---
title: Lösningar som använder globalt distribuerade analyser i Azure Cosmos DB.
description: Lär dig mer om de lösningar som kan skapas med hjälp av den globalt distribuerade analysen i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73681765"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Lösningar som använder globalt distribuerade analyser i Azure Cosmos DB

I den här artikeln beskrivs de lösningar som kan skapas med hjälp av den globalt distribuerade analysen i Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Detaljhandel och konsumentvaror

Du kan använda Spark-stöd i Azure Cosmos DB för att leverera rekommendationer och erbjudanden i realtid. Du kan hjälpa kunderna att upptäcka de objekt de behöver med anpassning i realtid och produktrekommendationer.

* Du kan använda det inbyggda machine learning-stödet från Apache Spark-körningen för att generera realtidsrekommendationer i produktkatalogerna.

* Du kan bryta klickströmsdata, köpa data och kunddata för att ge riktade rekommendationer som driver livstidsvärdet.

* Med hjälp av Azure Cosmos DB:s globala distributionsfunktion kan stora volymer produktdata som är spridda över regioner analyseras i millisekunder.

* Du kan snabbt få insikter för geografiskt distribuerade användare och data. Du kan förbättra konverteringsfrekvensen för kampanjer genom att visa rätt annons för rätt användare vid rätt tidpunkt.

* Du kan utnyttja den inbyggda Spark-streamingfunktionen för att berika livedata genom att kombinera den med statiska kunddata. På så sätt kan du leverera mer anpassade och riktade annonser i realtid och i sitt sammanhang med vad kunderna gör.

Följande avbildning visar hur Azure Cosmos DB Spark-stöd används för att optimera priser och kampanjer:

![Azure Cosmos DB:s Spark-stöd för att optimera priser och kampanjer](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Följande avbildning visar hur Azure Cosmos DB Spark-stöd används i rekommendationsmotorn i realtid:

![Azure Cosmos DB Spark-stöd i rekommendationsmotorn i realtid](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Tillverkning och IoT

Azure Cosmos DB:s inbyggda analysplattform gör att du kan aktivera realtidsanalys av IoT-data från miljontals enheter i global skala. Du kan göra moderna innovationer som att förutsäga vädermönster, prediktiv analys och energioptimeringar.

* Genom att använda Azure Cosmos DB kan du bryta data som tillgångsmått i realtid och väderfaktorer och sedan använda smart grid-analys för att optimera prestanda för anslutna enheter i fältet . Smart grid analytics är nyckeln till att kontrollera driftskostnaderna, förbättra nättillförlitligheten och leverera personliga energitjänster till konsumenterna.

Följande avbildning visar hur Azure Cosmos DB:s Spark-stöd används för att läsa mått från IoT-enheter och tillämpa smart grid-analys:

![Azure Cosmos DB:s Spark-stöd för att läsa mått från IoT-enheter](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Förebyggande underhåll

* Att underhålla tillgångar som kompressorer som används i små borriggar till djuphavsplattformar är en komplex strävan. Dessa tillgångar finns över hela världen och genererar petabyte data. Genom att använda Azure Cosmos DB kan du skapa en end-to-end prediktiv datapipeline som använder Spark-direktuppspelning för att bearbeta stora mängder sensortelemetri, lagra tillgångsdelar och sensormappningar data.

* Du kan skapa och distribuera machine learning-modeller för att förutsäga fel på tillgångar innan de inträffar och utfärda underhållsarbetsorder innan felet inträffar.

Följande avbildning visar hur Azure Cosmos DB:s Spark-support används för att skapa ett förutsägande underhållssystem:

![Azure Cosmos DB:s Spark-stöd för att skapa ett prediktivt underhållssystem](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Följande avbildning visar hur Azure Cosmos DB:s Spark-stöd används för att skapa ett fordonsdiagnossystem i realtid:

![Azure Cosmos DB:s Spark-stöd för att bygga ett fordonsdiagnossystem i realtid](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Spel

* Med det inbyggda Spark-stödet kan du med Azure Cosmos DB enkelt skapa, skala och distribuera avancerade analys- och maskininlärningsmodeller på några minuter för att skapa bästa möjliga spelupplevelse.

* Du kan analysera spelar-, inköps- och beteendedata för att skapa relevanta anpassade erbjudanden för att uppnå höga konverteringsfrekvenser.

* Med Spark maskininlärning kan du analysera och få insikter om speltelemetridata. Du kan diagnostisera och förhindra långsamma laddningstider och problem i spelet.

Följande avbildning visar hur Azure Cosmos DB:s Spark-support används i spelanalys:

![Azure Cosmos DB:s Spark-stöd i spelanalys](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om fördelarna med Azure Cosmos DB finns i [översiktsartikeln.](introduction.md)
* [Komma igång med Azure Cosmos DB API för MongoDB](mongodb-introduction.md)
* [Komma igång med Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [Komma igång med Azure Cosmos DB Gremlin API](graph-introduction.md)
* [Komma igång med Azure Cosmos DB Table API](table-introduction.md)
