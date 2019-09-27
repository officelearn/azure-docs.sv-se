---
title: Lösningar som använder globalt distribuerad analys i Azure Cosmos DB.
description: Lär dig mer om de lösningar som kan skapas med den globalt distribuerade analysen i Azure Cosmos DB.
author: rimman
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: d5c6b8727a24a7ea8ddf05f7983618b55884d3bf
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338989"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Lösningar som använder globalt distribuerad analys i Azure Cosmos DB

I den här artikeln beskrivs de lösningar som kan skapas med den globalt distribuerade analysen i Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Butiks-och konsument varor

Du kan använda Spark-support i Azure Cosmos DB för att tillhandahålla rekommendationer och erbjudanden i real tid. Du kan hjälpa kunderna att identifiera de objekt de behöver med anpassningar och produkt rekommendationer i real tid.

* Du kan använda det inbyggda Machine Learning support från Apache Spark runtime för att generera rekommendationer i real tid i alla produkt kataloger.

* Du kan klicka på strömma data, köpa data och kunddata för att tillhandahålla riktade rekommendationer som bedriver livs längd värde.

* Med hjälp av den globala distributions funktionen i Azure Cosmos DB kan stora mängder produkt data som sprids mellan regioner analyseras i millisekunder.

* Du kan snabbt få insikter om geografiskt distribuerade användare och data. Du kan förbättra konverterings takten för befordran genom att betjäna rätt AD till rätt användare vid rätt tidpunkt.

* Du kan utnyttja den inbyggda funktionen Spark streaming för att utöka real tids data genom att kombinera dem med statiska kunddata. På så sätt kan du leverera mer personliga och riktade annonser i real tid och i samband med vad kunderna gör.

Följande bild visar hur Azure Cosmos DB Spark-support används för att optimera priser och kampanjer:

![Azure Cosmos DB Spark-support för att optimera priser och kampanjer](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


Följande bild visar hur Azure Cosmos DB Spark-stöd används i real tids rekommendations motor:

![Stöd för Azure Cosmos DB spark i real tids rekommendations motor](./media/spark-api-introduction/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Tillverkning och IoT

Med Azure Cosmos DB inbyggda analys plattformen kan du aktivera real tids analys av IoT-data från miljon tals enheter i global skala. Du kan göra moderna innovationer som förutsäger väder mönster, förutsägelse analyser och energi optimeringar.

* Med hjälp av Azure Cosmos DB kan du mina data, till exempel real tids till gångs mått och väder faktorer, använda Smart Grid Analytics för att optimera prestanda för anslutna enheter i fältet. Smart Grid Analytics är nyckeln för att kontrol lera drifts kostnaderna, för att förbättra rutnätets tillförlitlighet och leverera anpassade energi tjänster till konsumenter.

Följande bild visar hur Azure Cosmos DB Spark-support används för att läsa mått från IoT-enheter och använda Smart Grid-analys:

![Azure Cosmos DB Spark-support för att läsa mått från IoT-enheter](./media/spark-api-introduction/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Förutsägande underhåll

* Att underhålla till gångar som kompressorer som används i små borrnings Rigs till plattformar med djup vatten är en komplex Endeavor. Dessa till gångar finns i hela världen och genererar petabyte av data. Med hjälp av Azure Cosmos DB kan du skapa en pipeline för förutsägelse data från slut punkt till slut punkt som använder Spark-direktuppspelning för att bearbeta stora mängder sensor telemetri, lagra till gångs delar och sensor mappnings data.

* Du kan skapa och distribuera maskin inlärnings modeller för att förutse till gångs fel innan de inträffar och utfärda underhålls arbets order innan felet uppstår.

Följande bild visar hur Azure Cosmos DB Spark-support används för att bygga ett förutsägande underhålls system:

![Azure Cosmos DB Spark-support för att bygga ett förutsägande underhålls system](./media/spark-api-introduction/predictive-maintenance-system.png)

Följande bild visar hur Azure Cosmos DB Spark-stödet används för att bygga ett real tids motor diagnos system:

![Azure Cosmos DB Spark-stöd för att bygga ett system för diagnostik av motor i real tid](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Spel

* Med inbyggd Spark-support gör Azure Cosmos DB att du enkelt kan bygga, skala och distribuera avancerade analyser och maskin inlärnings modeller på bara några minuter för att skapa bästa möjliga spel upplevelse.

* Du kan analysera spelare, Köp och beteende data för att skapa relevanta anpassade erbjudanden för att uppnå höga växelkurser.

* Med hjälp av Spark Machine Learning kan du analysera och få insikter om spel telemetridata. Du kan diagnostisera och förhindra långsamma inläsnings tider och problem med spelet.

Följande bild visar hur Azure Cosmos DB Spark-stödet används i spel analys:

![Azure Cosmos DB Spark-stöd i spel analys](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om fördelarna med Azure Cosmos DB finns i [översikts](introduction.md) artikeln.
* [Kom igång med Azure Cosmos DB API för MongoDB](mongodb-introduction.md)
* [Kom igång med Azure Cosmos DB API för Cassandra](cassandra-introduction.md)
* [Kom igång med API: et för Azure Cosmos DB Gremlin](graph-introduction.md)
* [Kom igång med Azure Cosmos DB Tabell-API](table-introduction.md)
