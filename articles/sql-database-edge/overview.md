---
title: Vad är Azure SQL Database Edge? | Microsoft Docs
description: Läs mer om Azure SQL Database Edge
keywords: Introduktion till SQL Database Edge, vad är SQL Database Edge, översikt över SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514009"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Vad är Azure SQL Database Edge Preview?

Azure SQL Database Edge Preview är en optimerad Relations databas motor som är avsedd för IoT-och IoT Edge-distributioner. Det innehåller funktioner för att skapa ett högpresterande lagrings-och bearbetnings lager för IoT-program och-lösningar. Azure SQL Database Edge tillhandahåller funktioner för att strömma, bearbeta och analysera Relations-och icke-relationella data, till exempel JSON, graf-och Time Series-data, vilket gör det till det rätta valet för en mängd moderna IoT-program.

Azure SQL Database Edge bygger på de senaste versionerna av [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), som ger branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Eftersom Azure SQL Database Edge bygger på samma motor som SQL Server och Azure SQL Database, innehåller den samma yta för T-SQL-program som gör det enklare och snabbare att utveckla program eller lösningar, och samtidigt gör programmet portabilitet mellan IoT Edge enheter, data Center och molnet rakt framåt.

## <a name="deployment-models"></a>Distributions modeller

Azure SQL Database Edge är tillgängligt på Azure Marketplace och kan distribueras som en modul för [Azure IoT Edge](../iot-edge/about-iot-edge.md). Mer information finns i [distribuera Azure SQL Database Edge](deploy-portal.md).<br>

![Diagram över SQL Database Edge-översikt](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Versioner av SQL Database Edge

SQL Database Edge är tillgängligt med tre olika utgåvor eller program varu planer. Dessa versioner har identiska funktions uppsättningar och skiljer sig bara åt när det gäller användnings rättigheter och hur mycket processor/minne som de stöder.

   |**Planera**  |**Beskrivning**  |
   |---------|---------|
   |Developer  |  Endast utvecklings-SKU: er, med samma begränsnings begränsningar som anges i standard-SKU: n som anges nedan |
   |Standard   |  Standard-planen har stöd för upp till 4 CPU: n och upp till 32 GB minne för SQL Database Edge-behållaren. |
   |Premium    |  Premium-SKU har stöd för upp till 8 kärnor och upp till 64 GB minne för SQL Database Edge-behållaren. |

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Azure SQL Database är för närvarande en för hands version. Mer information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Om du vill förstå funktions skillnaderna mellan Azure SQL Database kant och SQL Server, och skillnaderna mellan olika Azure SQL Database kant alternativ, se [SQL Database Edge-databas funktioner](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Strömmande funktioner  

Azure SQL Database Edge tillhandahåller inbyggda strömnings funktioner för analys i real tid och komplex händelse bearbetning. Strömnings funktionen skapas med samma konstruktioner som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och ger liknande funktioner som [Azure Stream Analytics på IoT Edge](../stream-analytics/stream-analytics-edge.md).

Strömnings motorn för Azure SQL Database Edge är utformad för låg latens, återhämtning, effektiv användning av bandbredd och efterlevnad.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Funktioner för Machine Learning och artificiell intelligens

Azure SQL Database Edge tillhandahåller inbyggda funktioner för maskin inlärning och analys genom att integrera ONNX för öppna format (Open neurala Network Exchange), vilket möjliggör utbyte av djup inlärnings-och neurala nätverks modeller mellan olika ramverk. Mer information om ONNX finns [här](https://onnx.ai/). ONNX runtime ger flexibiliteten att utveckla modeller på ett språk eller valfritt verktyg, som sedan kan konverteras till ONNX-formatet för körning inom SQL Database Edge. Mer information finns [i Machine Learning och artificiell intelligens med ONNX i SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Arbeta med Azure SQL Database Edge

Azure SQL Database Edge gör det enklare och mer produktivt att utveckla och underhålla program. Användarna kan använda alla välkända verktyg och kunskaper för att bygga fantastiska appar och lösningar för sina IoT Edge behov. Användaren kan utveckla i SQL Database Edge med hjälp av verktyg som följande:

- [Azure Portal](https://portal.azure.com/) -ett webbaserat program för att hantera alla Azure-tjänster.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – ett kostnads fritt, nedladdnings Bart klient program för att hantera alla SQL-infrastrukturer från SQL Server till SQL Database.
- [SQL Server data tools i Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) – ett kostnads fritt, nedladdnings Bart klient program för att utveckla SQL Server Relations databaser, SQL-databaser, Integration Services-paket, Analysis Services data modeller och repor ting Services-rapporter.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – ett kostnads fritt, nedladdnings Bart plattforms oberoende databas verktyg för data Professional med Microsoft-serien lokala och molnbaserade data plattformar på Windows, MacOS och Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) – en kostnads fri, nedladdnings bar kod redigerare med öppen källkod för Windows, MacOS och Linux. Det stöder tillägg, inklusive [MSSQL-tillägget](https://aka.ms/mssql-marketplace) för att fråga Microsoft SQL Server, Azure SQL Database och Azure SQL Data Warehouse.


## <a name="next-steps"></a>Nästa steg

- Information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Begäran om att aktivera Azure SQL Database Edge för din prenumeration.
- För att komma igång, se följande:
  - [Distribuera SQL Database Edge via Azure Portal](deploy-portal.md)
  - [Machine Learning och artificiell intelligens med SQL Database Edge](onnx-overview.md)
