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
ms.openlocfilehash: c8e3598e55d3f90ab2b7401380406677f56c0ce3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586488"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool"></a>Integrera andra tjänster med en Synapse SQL-pool

Med Funktionen Synapse SQL-pool i Azure Synapse Analytics kan användare integreras med många av de andra tjänsterna i Azure. Med Synapse SQL kan du skapa ett informationslager via dess SQL-poolresurs, som sedan kan använda flera ytterligare tjänster, varav några inkluderar:

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

Mer information finns i [Integrera med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en fullständigt hanterad analystjänst, som gör att du kan skapa invecklade modeller med hjälp av en stor uppsättning prediktiva verktyg. SQL-pool stöds som både källa och mål för dessa modeller och har följande funktioner:

* **Läs data:** Kör modeller i stor skala med T-SQL mot SQL-pool.
* **Skriv data:** Genomför ändringar från valfri modell tillbaka till SQL-pool.

Mer information finns i [Integrera med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics är en komplex, fullständigt hanterad infrastruktur för bearbetning och konsumtion av händelsedata som genereras från Azure Event Hub.  Integrering med SQL-pool gör det möjligt att effektivt bearbeta och lagra strömmande data tillsammans med relationsdata som möjliggör djupare och mer avancerad analys.  

* **Utdata för jobb:** Skicka utdata från Stream Analytics-jobb direkt till SQL-poolen.

Mer information finns i [Integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


