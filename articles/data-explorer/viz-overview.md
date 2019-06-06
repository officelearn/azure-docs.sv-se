---
title: Azure Data Explorer datavisualisering
description: Läs om hur du kan visualisera dina data i Datautforskaren i Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481841"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Datavisualisering med Azure Data Explorer 

Azure Data Explorer är en tjänst för Kunskapsutveckling för snabba och skalbara data för logg- och telemetri data som används för att skapa lösningar för avancerade analyser för stora mängder data. Azure Data Explorer kan integreras med olika visualiseringsverktyg, så att du kan visualisera dina data och dela resultatet i hela organisationen. Dessa data kan omvandlas till användbara insikter för att göra en inverkan på verksamheten.

Datavisualisering och rapportering är ett viktigt steg i processen för analys av data. Azure Data Explorer stöder många BI-tjänster så att du kan använda det som bäst passar ditt scenario och budget.

* Azure Data Explorer visualiseringar: Med Kusto-frågespråk den [ `render operator` ](/azure/kusto/query/renderoperator) erbjuder olika typer av visuella objekt om du vill visas frågeresultat. Fråga visualiseringar är användbara i avvikelseidentifiering och prognoser, maskininlärning och mer.

* [Power BI](https://powerbi.microsoft.com): Azure Data Explorer gör möjligheten att ansluta till Power BI med olika metoder: 

  * [Inbyggda interna Power BI-anslutningen](/azure/data-explorer/power-bi-connector)

  * [Fråga efter import från Azure Data Explorer till Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-fråga](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Azure Data Explorer ger möjlighet att ansluta till Excel med hjälp av den inbyggda interna Excel-anslutningen eller importera en fråga från Azure Data Explorer till Excel.

* [Grafana](https://grafana.com): Grafana innehåller ett plugin-program i Datautforskaren i Azure som hjälper dig att visualisera data från Azure Data Explorer. Du [konfigurera Datautforskaren i Azure som en datakälla för Grafana och visualisera data](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Azure Data Explorer ger möjlighet att ansluta till Sisense med JDBC-kopplingen. Du [konfigurera Datautforskaren i Azure som en datakälla för Sisense och visualisera data](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Azure Data Explorer ger möjlighet att ansluta till Tableau med den [ODBC-anslutningsprogram och visualisera data i Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Azure Data Explorer ger möjlighet att ansluta till Qlik med den [ODBC-anslutningsprogram](/azure/data-explorer/connect-odbc).