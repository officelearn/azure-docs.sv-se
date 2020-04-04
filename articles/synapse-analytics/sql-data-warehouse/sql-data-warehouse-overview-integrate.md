---
title: Skapa integrerade lösningar
description: Lösningsverktyg och partners som integreras med en Synapse SQL-pool.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633132"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrera andra tjänster med ett SQL Analytics-datalager

SQL Analytics-funktionen i Azure Synapse Analytics gör det möjligt för användare att integrera med många av de andra tjänsterna i Azure. Med SQL Analytics kan du skapa ett informationslager via dess SQL Pool-resurs, som sedan kan använda flera ytterligare tjänster, varav några inkluderar:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Mer information om integrationstjänster i Azure finns i artikeln [integrationspartner.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

Med Power BI-integrering kan du kombinera beräkningskraften i ett informationslager med dynamisk rapportering och visualisering av Power BI. Power BI-integrering innehåller för närvarande:

* **Direct Connect**: En mer avancerad anslutning med logisk pushdown mot ett informationslager som etablerats med SQL-pool. Pushdown ger snabbare analys i större skala.
* **Öppna i Power BI:** Knappen "Öppna i Power BI" skickar instansinformation till Power BI för ett förenklat sätt att ansluta.

Mer information finns i [Integrera med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)eller Power [BI-dokumentationen](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory ger användarna en hanterad plattform för att skapa komplexa extrahera och ladda pipelines. Integrering av SQL-pool med Azure Data Factory innehåller:

* **Lagrade procedurer**: Dirigera utförandet av lagrade procedurer.
* **Kopiera**: Använd ADF för att flytta data till SQL-poolen. Den här åtgärden kan använda ADF:s standardmekanism för datarörelser eller PolyBase under täcken.

Mer information finns i [Integrera med Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en fullständigt hanterad analystjänst, som gör att du kan skapa invecklade modeller med hjälp av en stor uppsättning prediktiva verktyg. SQL-pool stöds som både källa och mål för dessa modeller och har följande funktioner:

* **Läs data:** Kör modeller i stor skala med T-SQL mot SQL-pool.
* **Skriv data:** Genomför ändringar från valfri modell tillbaka till SQL-pool.

Mer information finns i [Integrera med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics är en komplex, fullständigt hanterad infrastruktur för bearbetning och konsumtion av händelsedata som genereras från Azure Event Hub.  Integrering med SQL-pool gör det möjligt att effektivt bearbeta och lagra strömmande data tillsammans med relationsdata som möjliggör djupare och mer avancerad analys.  

* **Utdata för jobb:** Skicka utdata från Stream Analytics-jobb direkt till SQL-poolen.

Mer information finns i [Integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
