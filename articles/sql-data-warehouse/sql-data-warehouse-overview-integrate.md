---
title: Bygg integrerade lösningar
description: Verktyg och partner med lösningar som integreras med Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685643"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrera andra tjänster med SQL Data Warehouse
Förutom de viktigaste funktionerna gör SQL Data Warehouse att användarna kan integreras med många av de andra tjänsterna i Azure. Några av dessa tjänster är:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse fortsätter att integrera med fler tjänster i Azure och fler [integrations partner](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Med Power BI integration kan du kombinera beräknings kraften hos SQL Data Warehouse med den dynamiska rapporteringen och visualiseringen av Power BI. Power BI-integrering innehåller för närvarande:

* **Direkt anslutning**: en mer avancerad anslutning med logiska mottagnings mot SQL Data Warehouse. Mottagnings ger snabbare analys på en större skala.
* **Öppna i Power BI**: med knappen "öppna i Power BI" skickas instans information till Power BI för ett förenklat sätt att ansluta.

Mer information finns i [integrera med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)eller [Power BI-dokumentationen](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ger användare en hanterad plattform för att skapa komplexa extraherings-och belastnings pipeliner. SQL Data Warehouse s integrering med Azure Data Factory innehåller:

* **Lagrade procedurer**: dirigera körningen av lagrade procedurer på SQL Data Warehouse.
* **Kopiera**: Använd ADF för att flytta data till SQL Data Warehouse. Den här åtgärden kan använda ADF: s standard funktion för data förflyttning eller PolyBase under försättsblad. 

Mer information finns i [integrera med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en helt hanterad analys tjänst som gör att du kan skapa invecklade modeller med en stor uppsättning av förutsägande verktyg. SQL Data Warehouse stöds både som källa och mål för dessa modeller med följande funktioner:

* **Läs data:** Enhets modeller i skala med hjälp av T-SQL mot SQL Data Warehouse.
* **Skriv data:** Genomför ändringar från alla modeller tillbaka till SQL Data Warehouse.

Mer information finns i [integrera med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics är en komplex, fullständigt hanterad infrastruktur för bearbetning och användning av händelse data som genereras från Azure Event Hub.  Integrering med SQL Data Warehouse gör att strömmande data kan bearbetas och lagras tillsammans med relations data som möjliggör djupare och mer avancerad analys.  

* **Jobbets utdata:** Skicka utdata från Stream Analytics-jobb direkt till SQL Data Warehouse.

Mer information finns i [integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


