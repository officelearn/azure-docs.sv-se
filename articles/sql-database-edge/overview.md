---
title: Vad är Azure SQL Database Edge? | Microsoft Docs
description: Lär dig mer om Azure SQL Database Edge
keywords: introduktion till sql-databaskant,vad är sql-databaskant, sql-databaskantöversikt
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246712"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Vad är förhandsversionen av Azure SQL Database Edge?

Azure SQL Database Edge Preview är en optimerad relationsdatabasmotor som är anpassad för IoT- och IoT Edge-distributioner. Det ger funktioner för att skapa ett högpresterande datalagrings- och bearbetningslager för IoT-program och lösningar. Azure SQL Database Edge tillhandahåller funktioner för att strömma, bearbeta och analysera relationella och icke-relationella som JSON, diagram och tidsseriedata, vilket gör det till rätt val för en mängd moderna IoT-program.

Azure SQL Database Edge bygger på de senaste versionerna av [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), som ger branschledande prestanda, säkerhet och frågebearbetning. Eftersom Azure SQL Database Edge är byggt på samma motor som SQL Server och Azure SQL Database, ger det samma T-SQL-programmeringsyta som gör utvecklingen av program eller lösningar enklare och snabbare, och samtidigt gör programmet portabilitet mellan IoT Edge-enheter, datacenter och molnet rakt fram.

## <a name="deployment-models"></a>Distributionsmodeller

Azure SQL Database Edge är tillgängligt på Azure Marketplace och kan distribueras som en modul för [Azure IoT Edge](../iot-edge/about-iot-edge.md). Mer information finns i [Distribuera Azure SQL Database Edge](deploy-portal.md).<br>

![Översiktsdiagram för SQL Database Edge](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Utgåvor av SQL Database Edge

SQL Database Edge finns med tre olika utgåvor eller programvaruplaner. Dessa utgåvor har identiska funktionsuppsättningar och skiljer sig bara i fråga om deras användningsrättigheter och mängden cpu / minne de stöder.

   |**Planera**  |**Beskrivning**  |
   |---------|---------|
   |Utvecklare av Azure SQL Database Edge  |  Utveckling endast sku, varje SQL Database Edge behållare är begränsad till upp till 4 kärnor och 32 GB minne  |
   |Azure SQL Database Edge    |  Produktion sku, varje SQL Database Edge behållare är begränsad till upp till 8 kärnor och 64 GB minne . |

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Azure SQL Database är för närvarande i förhandsversion. Mer information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Information om hur du förstår funktionsskillnaderna mellan Azure SQL Database Edge och SQL Server, samt skillnaderna mellan olika Azure SQL Database Edge-alternativ, finns i [sql Database Edge Database-funktioner](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Streaming-funktioner  

Azure SQL Database Edge innehåller inbyggda streamingfunktioner för analys i realtid och komplex händelsebearbetning. Streamingfunktionen är byggd med samma konstruktioner som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och ger liknande funktioner som Azure Stream Analytics på [IoT Edge](../stream-analytics/stream-analytics-edge.md).

Strömningsmotorn för Azure SQL Database Edge är utformad för låg latens, återhämtning, effektiv användning av bandbredd och efterlevnad.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Maskininlärning och artificiell intelligens kapacitet

Azure SQL Database Edge tillhandahåller inbyggda funktioner för maskininlärning och analys genom att integrera onnx-körningen (Open Neural Network Exchange) i öppet format, vilket möjliggör utbyte av djupinlärnings- och neurala nätverksmodeller mellan olika ramverk. För mer information om ONNX, se [här](https://onnx.ai/). ONNX-körning ger flexibiliteten att utveckla modeller på ett språk eller verktyg som du väljer, som sedan kan konverteras till ONNX-formatet för körning inom SQL Database Edge. Mer information finns i [Maskininlärning och artificiell intelligens med ONNX i SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Arbeta med Azure SQL Database Edge

Azure SQL Database Edge gör det enklare och mer produktivt att utveckla och underhålla program. Användare kan använda alla välbekanta verktyg och färdigheter för att skapa bra appar och lösningar för sina IoT Edge-behov. Användaren kan utvecklas i SQL Database Edge med hjälp av verktyg som följande:

- [Azure-portalen](https://portal.azure.com/) - Ett webbaserat program för att hantera alla Azure-tjänster.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Ett kostnadsfritt, nedladdningsbart klientprogram för hantering av alla SQL-infrastrukturer, från SQL Server till SQL Database.
- [SQL Server Data Tools i Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) - Ett kostnadsfritt, nedladdningsbart klientprogram för utveckling av SQL Server relationsdatabaser, SQL-databaser, Integration Services-paket, Analysis Services-datamodeller och Reporting Services-rapporter.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – Ett kostnadsfritt, nedladdningsbart databasverktyg som inte är plattformens plattformsgränserna för datatekniker med hjälp av Microsoft-familjen av lokala dataplattformar och molndataplattformar på Windows, MacOS och Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - En gratis, nedladdningsbar, öppen källkodsredigerare för Windows, macOS och Linux. Den stöder tillägg, inklusive [mssql-tillägget](https://aka.ms/mssql-marketplace) för att fråga Microsoft SQL Server, Azure SQL Database och Azure SQL Data Warehouse.


## <a name="next-steps"></a>Nästa steg

- Information om priser och tillgänglighet finns i [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Begär att aktivera Azure SQL Database Edge för din prenumeration.
- Kom igång genom att se följande:
  - [Distribuera SQL Database Edge via Azure-portalen](deploy-portal.md)
  - [Maskininlärning och artificiell intelligens med SQL Database Edge](onnx-overview.md)
