---
title: Visualisering av Azure Data Explorer-data
description: Lär dig mer om olika sätt att visualisera dina Azure Data Explorer-data
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139070"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Datavisualisering med Azure Data Explorer 

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata som används för att skapa komplexa analyslösningar för stora mängder data. Azure Data Explorer integreras med olika visualiseringsverktyg, så att du kan visualisera dina data och dela resultaten i hela organisationen. Dessa data kan omvandlas till användbara insikter för att påverka ditt företag.

Datavisualisering och rapportering är ett viktigt steg i dataanalysprocessen. Azure Data Explorer stöder många BI-tjänster så att du kan använda den som bäst passar ditt scenario och din budget.

## <a name="kusto-query-language-visualizations"></a>Kusto frågespråkvisualiseringar

Frågespråket [`render operator`](/azure/kusto/query/renderoperator) Kusto erbjuder olika visualiseringar, till exempel tabeller, cirkeldiagram och stapeldiagram för att visa frågeresultat. Frågevisualiseringar är användbara vid avvikelseidentifiering och prognostisering, maskininlärning med mera.

## <a name="power-bi"></a>Power BI

Azure Data Explorer ger möjlighet att ansluta till [Power BI](https://powerbi.microsoft.com) med olika metoder: 

  * [Inbyggd inbyggd Power BI-kontakt](/azure/data-explorer/power-bi-connector)

  * [Frågeimport från Azure Data Explorer till Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-fråga](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer ger möjlighet att ansluta till [Microsoft Excel](https://products.office.com/excel) med den [inbyggda inbyggda Excel-anslutningen](excel-connector.md)eller [importera en fråga](excel-blank-query.md) från Azure Data Explorer till Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) tillhandahåller ett Plugin-program för Azure Data Explorer som gör att du kan visualisera data från Azure Data Explorer. Du [konfigurerar Azure Data Explorer som en datakälla för Grafana och visualiserar sedan data](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

Azure Data Explorer ger möjlighet att ansluta till [Kibana (upptäck sidan)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) med K2Bridge, en anslutningsapp med öppen källkod. Du [konfigurerar Azure Data Explorer som en datakälla för Kibana och visualiserar sedan data](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>ODBC-anslutningsapp

Azure Data Explorer tillhandahåller en [ODBC-anslutningsapp (Open Database Connectivity)](connect-odbc.md) så att alla program som stöder ODBC kan ansluta till Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer ger möjlighet att ansluta till [Tableau](https://www.tableau.com) med [ODBC-kopplingen](/azure/data-explorer/connect-odbc) och sedan [visualisera data i Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer ger möjlighet att ansluta till [Qlik](https://www.qlik.com) med [ODBC-kopplingen](/azure/data-explorer/connect-odbc) och sedan skapa Qlik Sense-instrumentpaneler och visualisera data. Med hjälp av följande video kan du lära dig att visualisera Azure Data Explorer-data med Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer ger möjlighet att ansluta till [Sisense](https://www.sisense.com) med JDBC-anslutningen. Du [konfigurerar Azure Data Explorer som en datakälla för Sisense och visualiserar sedan data](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Du kan använda [Redash](https://redash.io/) för att skapa instrumentpaneler och visualisera data. [Konfigurera Azure Data Explorer som en datakälla för Redash och visualisera sedan data](/azure/data-explorer/redash).