---
title: "Konfigurera datakällor i OMS Log Analytics | Microsoft Docs"
description: "Datakällor definierar vilka data att logganalys samlar in från agenter och andra anslutna källor.  Den här artikeln beskriver konceptet med hur Log Analytics använder datakällor beskrivs detaljer om hur du konfigurerar dem och innehåller en översikt över olika datakällor."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: 00d030a502cf70ea9a5dea767f560cdf2919573e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="data-sources-in-log-analytics"></a>Datakällor i logganalys
Logganalys samlar in data från den anslutna källor i OMS-arbetsytan och lagrar den i OMS-databasen.  De data som samlas in från varje definieras av datakällor som du konfigurerar.  Data i OMS-databasen lagras som en uppsättning poster.  Varje datakälla skapar poster för en viss typ med varje typ med en egen uppsättning egenskaper.

![Logga Analytics datainsamling](./media/log-analytics-data-sources/overview.png)

Datakällor är annat än OMS-lösningar som också samla in data från anslutna källor och skapa poster i OMS-databasen.  Lösningar kan läggas till arbetsytan från galleriet lösningar och vanligtvis ger ytterligare analysverktyg i OMS-portalen.  

## <a name="summary-of-data-sources"></a>Översikt över datakällor
I följande tabell visas de datakällor som är tillgängliga i logganalys.  Varje har en länk till en separat artikel som tillhandahåller information för datakällan.

| Datakälla | Händelsetyp | Beskrivning |
|:--- |:--- |:--- |
| [Anpassade loggar](log-analytics-data-sources-custom-logs.md) |\<Loggnamn\>_CL |Textfiler på Windows- eller Linux-agenter som innehåller informationen i felloggen. |
| [Windows-händelseloggar](log-analytics-data-sources-windows-events.md) |Händelse |Händelser som samlas in från händelseloggen på Windows-datorer. |
| [Windows-prestandaräknare](log-analytics-data-sources-performance-counters.md) |Perf |Prestandaräknare som samlats in från Windows-datorer. |
| [Linux-prestandaräknare](log-analytics-data-sources-performance-counters.md) |Perf |Prestandaräknare som samlats in från Linux-datorer. |
| [IIS-loggar](log-analytics-data-sources-iis-logs.md) |W3CIISLog |Internet Information Services loggar i W3C-format. |
| [Syslog](log-analytics-data-sources-syslog.md) |Syslog |Syslog-händelser på Windows- eller Linux-datorer. |

## <a name="configuring-data-sources"></a>Konfigurera datakällor
Du konfigurerar datakällor från den **Data** -menyn i logganalys **inställningar**.  Ingen konfiguration levereras till alla anslutna källor i OMS-arbetsyta.  Du kan för närvarande utesluta alla eventuella agenter från den här konfigurationen.

![Konfigurera Windows-händelser](./media/log-analytics-data-sources/configure-events.png)

1. I OMS-konsolen klickar du på den **inställningar** panelen eller **inställningar** längst upp på skärmen.
2. Välj **Data**.
3. Klicka på datakällan för att konfigurera.
4. Följ länken i dokumentationen för varje datakälla i tabellen ovan information på deras konfiguration.

> [!NOTE]
> Du kan konfigurera logganalys datakällor för närvarande i Azure-portalen.

## <a name="data-collection"></a>Datainsamling
Konfigurationer för datakällan levereras till agenter som är anslutna direkt till Log Analytics inom några minuter.  Angivna data samlas in från agenten och levereras direkt till Log Analytics med intervall som är specifika för varje datakälla.  Finns i dokumentationen för varje datakälla som innehåller dessa uppgifter.

System Center Operations Manager (SCOM) agenter i en ansluten hanteringsgrupp, konfigurationer för datakällan översättas till hanteringspaket och levereras till hanteringsgruppen var femte minut som standard.  Agenten hämtar management pack som andra och samlar in angivna data. Beroende på datakällan kommer att antingen skickas till en hanteringsserver som vidarebefordrar data till Log Analytics eller agenten skickar data till logganalys utan att gå via management-servern. Referera till [data samling information om OMS-funktioner och lösningar](log-analytics-add-solutions.md#data-collection-details) mer information.  Du kan läsa om information om ansluta SCOM och OMS och ändra frekvensen konfigurationen levereras på [konfigurerar integrering med System Center Operations Manager](log-analytics-om-agents.md).

Om agenten inte kan ansluta till logganalys eller Operations Manager fortsätter den att samla in data som den levererar när den upprättar en anslutning.  Data kan gå förlorade om mängden data når maximal cachestorlek för klienten, eller om agenten inte kan upprätta en anslutning inom 24 timmar.

## <a name="log-analytics-records"></a>Log Analytics-poster
Alla data som samlas in av logganalys lagras i OMS-databasen som poster.  Innehåller information som samlas in av olika datakällor har sin egen uppsättning egenskaper och identifieras genom sina **typen** egenskapen.  Finns i dokumentationen för varje datakälla och lösning för information om varje typ av post.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [lösningar](log-analytics-add-solutions.md) att lägga till funktioner till logganalys och också samla in data till OMS-databasen.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.  
* Konfigurera [aviseringar](log-analytics-alerts.md) att proaktivt meddela dig om viktiga data som samlas in från datakällor och lösningar.
