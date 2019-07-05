---
title: Azure Data Explorer datavisualisering
description: Läs om hur du kan visualisera dina data i Datautforskaren i Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536722"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Datavisualisering med Azure Data Explorer 

Azure Data Explorer är en tjänst för Kunskapsutveckling för snabba och skalbara data för logg- och telemetri data som används för att skapa lösningar för avancerade analyser för stora mängder data. Azure Data Explorer kan integreras med olika visualiseringsverktyg, så att du kan visualisera dina data och dela resultatet i hela organisationen. Dessa data kan omvandlas till användbara insikter för att göra en inverkan på verksamheten.

Datavisualisering och rapportering är ett viktigt steg i processen för analys av data. Azure Data Explorer stöder många BI-tjänster så att du kan använda det som bäst passar ditt scenario och budget.

## <a name="kusto-query-language-visualizations"></a>Kusto-fråga språk visualiseringar

Kusto-frågespråket [ `render operator` ](/azure/kusto/query/renderoperator) erbjuder olika visualiseringar som tabeller, cirkeldiagram och stapeldiagram till att avbilda frågeresultat. Fråga visualiseringar är användbara i avvikelseidentifiering och prognoser, maskininlärning och mer.

## <a name="power-bi"></a>Power BI

Azure Data Explorer gör det möjligt att ansluta till [Power BI](https://powerbi.microsoft.com) med olika metoder: 

  * [Inbyggda interna Power BI-anslutningen](/azure/data-explorer/power-bi-connector)

  * [Fråga efter import från Azure Data Explorer till Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-fråga](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer gör det möjligt att ansluta till [Microsoft Excel](https://products.office.com/excel) med hjälp av det inbyggda interna anslutningsapp för Excel- eller importera en fråga från Azure Data Explorer till Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) innehåller ett plugin-program i Datautforskaren i Azure som hjälper dig att visualisera data från Azure Data Explorer. Du [konfigurera Datautforskaren i Azure som en datakälla för Grafana och visualisera data](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>ODBC-anslutningsapp

Azure Data Explorer tillhandahåller en [Open Database Connectivity (ODBC) connector](connect-odbc.md) så att alla program som har stöd för ODBC kan ansluta till Datautforskaren i Azure.

## <a name="tableau"></a>Tableau

Azure Data Explorer gör det möjligt att ansluta till [Tableau](https://www.tableau.com) med hjälp av den [ODBC-anslutningsprogram](/azure/data-explorer/connect-odbc) och sedan [visualisera data i Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer gör det möjligt att ansluta till [Qlik](https://www.qlik.com) med hjälp av den [ODBC-anslutningsprogram](/azure/data-explorer/connect-odbc) och skapa Qlik Sense instrumentpaneler och visualisera data. Med hjälp av följande video, du kan lära dig att visualisera data i Datautforskaren i Azure med Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer gör det möjligt att ansluta till [Sisense](https://www.sisense.com) med JDBC-kopplingen. Du [konfigurera Datautforskaren i Azure som en datakälla för Sisense och visualisera data](/azure/data-explorer/sisense).