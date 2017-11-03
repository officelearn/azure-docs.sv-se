---
title: "Skapa integrerad lösningar med SQL Data Warehouse | Microsoft Docs"
description: "Verktyg och partners med lösningar som kan integreras med SQL Data Warehouse. "
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: d407c29f99fd7537590ec787febd84a9e3f4f353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>Dra nytta av andra tjänster med SQL Data Warehouse
Utöver dess kärnfunktioner kan SQL Data Warehouse du utnyttja många av andra tjänster i Azure tillsammans med den.  Vi har särskilt för närvarande vidtagit åtgärder för att integrerar med följande:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Vi arbetar för att ansluta med fler tjänster i Azure-ekosystemet.

## <a name="power-bi"></a>Power BI
Power BI-integrering kan du utnyttja datorkraft för SQL Data Warehouse med dynamiska rapportering och visualisering av Power BI. Power BI-integrering innehåller för närvarande:

* **Direct Connect**: en mer avancerad anslutning med logiska pushdown mot SQL Data Warehouse.  Det ger snabbare analys av en större skala.
* **Öppna i Power BI**: knappen ”Öppna i Power BI-instansinformation skickar till Power BI, vilket ger en mer sömlös anslutning.

Se [integrera med Power BI](sql-data-warehouse-integrate-power-bi.md) eller [Power BI-dokumentationen](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) för mer information.

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ger användarna en hanterad plattform för att skapa komplexa extrahera belastningen pipelines.  SQL Data Warehouse-integrering med Azure Data Factory omfattar följande:

* **Lagrade procedurer**: Dirigera körningen av lagrade procedurer i SQL Data Warehouse.
* **Kopiera**: Använd ADF att flytta data till SQL Data Warehouse.  Den här åtgärden kan använda ADF: s standard data movement mekanism eller PolyBase under försättsbladen. 

Se [integrera med Azure Data Factory](sql-data-warehouse-integrate-azure-data-factory.md) eller [dokumentation för Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) för mer information.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en helt hanterad analytics-tjänst som låter användare skapa komplicerade modeller som utnyttjar en stor mängd förutsägande verktyg.  SQL Data Warehouse stöds som en källa och ett mål för dessa modeller med följande funktioner:

* **Läs Data:** enhet modeller i stor skala med T-SQL mot SQL Data Warehouse.
* **Skriva Data:** Commit ändras från någon modell tillbaka till SQL Data Warehouse.

Se [integrera med Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) eller [Azure Machine Learning dokumentationen](https://azure.microsoft.com/services/machine-learning/) för mer information.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics är en komplex, helt hanterad infrastruktur för bearbetning och förbrukar händelsedata genererade från Azure Event Hub.  Integrering med SQL Data Warehouse kan för strömmande data för att effektivt ska bearbetas och lagras tillsammans med relationsdata aktivera djupare mer avancerad analys.  

* **Jobbutdata:** skicka utdata från Stream Analytics-jobb direkt till SQL Data Warehouse.

Se [integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md) eller [Azure Stream Analytics dokumentationen](https://azure.microsoft.com/documentation/services/stream-analytics/) för mer information.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
