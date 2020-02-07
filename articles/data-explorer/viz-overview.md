---
title: Data visualisering för Azure Datautforskaren
description: Lär dig hur du kan visualisera dina Azure Datautforskaren-data på olika sätt
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 32ccc5ad236c87d3609798a8432db14ee440d067
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064571"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Data visualisering med Azure Datautforskaren 

Azure Datautforskaren är en snabb och mycket skalbar data utforsknings tjänst för logg-och telemetridata som används för att bygga komplexa analys lösningar för stora mängder data. Azure Datautforskaren integreras med olika visualiserings verktyg, så att du kan visualisera dina data och dela resultaten i hela organisationen. Dessa data kan omvandlas till åtgärds bara insikter för att påverka din verksamhet.

Data visualisering och rapportering är ett viktigt steg i processen för data analys. Azure Datautforskaren stöder många BI-tjänster så att du kan använda den som passar bäst för ditt scenario och din budget.

## <a name="kusto-query-language-visualizations"></a>Visualiseringar för Kusto-frågespråk

Kusto-frågespråket [`render operator`](/azure/kusto/query/renderoperator) erbjuder olika visualiseringar som tabeller, cirkel diagram och stapeldiagram för att åskådliggöra frågeresultaten. Fråga visualiseringar är till hjälp vid avvikelse identifiering och prognostisering, maskin inlärning och mycket annat.

## <a name="power-bi"></a>Power BI

Azure Datautforskaren ger möjlighet att ansluta till [Power BI](https://powerbi.microsoft.com) på olika sätt: 

  * [Inbyggd intern Power BI anslutning](/azure/data-explorer/power-bi-connector)

  * [Fråga om import från Azure Datautforskaren till Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-fråga](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Datautforskaren ger möjlighet att ansluta till [Microsoft Excel](https://products.office.com/excel) med hjälp av den [inbyggda inbyggda Excel-anslutningen](excel-connector.md)eller [importera en fråga](excel-blank-query.md) från Azure datautforskaren till Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) tillhandahåller ett Azure datautforskaren-plugin-program som gör att du kan visualisera data från Azure datautforskaren. Du [konfigurerar Azure datautforskaren som en data källa för Grafana och visualiserar sedan data](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>ODBC-anslutningsapp

Azure Datautforskaren tillhandahåller en [Open Database Connectivity-anslutning (ODBC)](connect-odbc.md) så att alla program som stöder ODBC kan ansluta till Azure-datautforskaren.

## <a name="tableau"></a>Tableau

Azure Datautforskaren ger möjlighet att ansluta till [Tableau](https://www.tableau.com) med hjälp av [ODBC-anslutningen](/azure/data-explorer/connect-odbc) och sedan [visualisera data i Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Datautforskaren ger möjlighet att ansluta till [Qlik](https://www.qlik.com) med hjälp av [ODBC-anslutningen](/azure/data-explorer/connect-odbc) och sedan skapa Qlik-språkinstrument paneler och visualisera data. Med hjälp av följande video kan du lära dig att visualisera Azure Datautforskaren data med Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Datautforskaren ger möjlighet att ansluta till [Sisense](https://www.sisense.com) med hjälp av JDBC-anslutningen. Du [konfigurerar Azure datautforskaren som en data källa för Sisense och visualiserar sedan data](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Du kan använda [rebindestreck](https://redash.io/) för att bygga instrument paneler och visualisera data. [Konfigurera Azure datautforskaren som en data källa för streck och visualisera sedan data](/azure/data-explorer/redash).