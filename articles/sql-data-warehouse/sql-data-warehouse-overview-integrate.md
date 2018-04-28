---
title: Skapa integrerad lösningar med SQL Data Warehouse | Microsoft Docs
description: 'Verktyg och partners med lösningar som kan integreras med SQL Data Warehouse. '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrera andra tjänster med SQL Data Warehouse
Utöver dess kärnfunktioner kan SQL Data Warehouse du integrera med många andra tjänster i Azure. Några av dessa tjänster är:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse fortsätter att integreras med fler tjänster i Azure och mycket mer [integrering partners](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI-integrering kan du kombinera datorkraft för SQL Data Warehouse med dynamiska rapportering och visualisering av Power BI. Power BI-integrering innehåller för närvarande:

* **Direct Connect**: en mer avancerad anslutning med logiska pushdown mot SQL Data Warehouse. Pushdown ger snabbare analys i större skala.
* **Öppna i Power BI**: knappen ”Öppna i Power BI-skickar information om processinstans till Power BI för ett simplifed sätt att ansluta.

Mer information finns i [integrera med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), eller [Power BI-dokumentationen](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ger användarna en hanterad plattform för att skapa komplexa extrahera och hämta pipelines. SQL Data Warehouse-integrering med Azure Data Factory innehåller:

* **Lagrade procedurer**: Dirigera körningen av lagrade procedurer i SQL Data Warehouse.
* **Kopiera**: Använd ADF att flytta data till SQL Data Warehouse. Den här åtgärden kan använda ADF: s standard data movement mekanism eller PolyBase under försättsbladen. 

Mer information finns i [integrera med Azure Data Factory](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en helt hanterad analytics-tjänsten där du kan skapa komplicerade modeller med en stor mängd förutsägande verktyg. SQL Data Warehouse stöds som en källa och ett mål för dessa modeller med följande funktioner:

* **Läs Data:** enhet modeller i stor skala med T-SQL mot SQL Data Warehouse.
* **Skriva Data:** Commit ändras från någon modell tillbaka till SQL Data Warehouse.

Mer information finns i [integrera med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics är en komplex, helt hanterad infrastruktur för bearbetning och förbrukar händelsedata genererade från Azure Event Hub.  Integrering med SQL Data Warehouse kan för strömmande data för att effektivt ska bearbetas och lagras tillsammans med relationsdata aktivera djupare mer avancerad analys.  

* **Jobbutdata:** skicka utdata från Stream Analytics-jobb direkt till SQL Data Warehouse.

Mer information finns i [integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Nästa steg
Om du vill integrera med Azure SQL Database finns [Konfigurera SQL elastisk databasfrågan](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

