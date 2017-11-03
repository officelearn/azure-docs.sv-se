---
title: Visa analytiska data i Azure Web Apps | Microsoft Docs
description: "Du kan använda Azure Web Apps Analytics-lösning och få insikter om dina Azure-webbprogram genom att samla in olika mått för alla webbprogram i Azure-resurser."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.openlocfilehash: 9ef26d4b6bfd92925a70b7bbdf8979e287c73445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Visa analytiska data för mått för alla webbprogram i Azure-resurser

![Web Apps symbol](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
Lösningen Azure Web Apps Analytics (förhandsversion) ger insikter om din [Azure Web Apps](../app-service/app-service-web-overview.md) genom att samla in olika mått för alla webbprogram i Azure-resurser. Med lösningen kan du analysera och söka efter måttdata för web app resurs.

Med lösningen, kan du visa den:

- Den översta Web Apps med högsta svarstid
- Antalet begäranden i dina webbprogram, inklusive lyckade och misslyckade begäranden
- Den översta Web Apps med högsta inkommande och utgående trafik
- Övre serviceplaner med hög användning av CPU och minne
- Loggen åtgärder i Azure Web Apps aktivitet

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra logganalys-lösningar är inte samlas in för Azure Web Apps av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](log-analytics-windows-agents.md) | Nej | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM-hanteringsgrupp](log-analytics-om-agents.md) | Nej | Lösningen samlar inte in information från agenter i en ansluten SCOM-hanteringsgrupp. |
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Lösningen matchar inte samlingsinformation från Azure storage. |

## <a name="prerequisites"></a>Krav

- Du måste ha en Azure-prenumeration för att komma åt Azure Web App resursinformation mått.

## <a name="configuration"></a>Konfiguration

Utför följande steg för att konfigurera Azure Web Apps Analytics lösningen för dina arbetsytor.

1. Aktivera Azure Web Apps Analytics-lösning från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. [Aktivera loggning för Azure-resurs mått till OMS med hjälp av PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

Azure Web Apps Analytics lösningen samlar in två olika mått från Azure:

- Azure Web Apps-mått
  - Genomsnittlig minne arbetsminne
  - Genomsnittlig svarstid
  - Byte mottagna/skickas
  - CPU-tid
  - Begäranden
  - Minne arbetsminne
  - Httpxxx
- App Service-Plan mått
  - Byte mottagna/skickas
  - CPU-procent
  - Diskkölängd
  - HTTP-Kölängd
  - Minnesprocent

App Service-Plan mått samlas endast in om du använder en särskild service-plan. Detta gäller inte för kostnadsfri eller delad App Service-planer.

Om du lägger till lösningen med OMS-portalen, visas följande sida vid sida. Du behöver [aktivera loggning för Azure-resurs mått till OMS med hjälp av PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

![Utför Assessment meddelande](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

När du har konfigurerat lösningen ska data börjar flöda till din arbetsyta inom 15 minuter.

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till Azure Web Apps Analytics lösningen till din arbetsyta i **Azure Web Apps Analytics** panel har lagts till översikt över instrumentpanelen. Den här panelen visar antalet Azure Web Apps att lösningen har åtkomst till i din Azure-prenumeration.

![Azure Web Apps Analytics sida vid sida](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Visa information om Azure Web Apps Analytics

Klicka på den **Azure Web Apps Analytics** öppna den **Azure Web Apps Analytics** instrumentpanelen. Instrumentpanelen innehåller bladen i följande tabell. Varje bladet visar upp till tio objekt som matchar det bladet villkoren för angivet omfång och tidsintervall. Du kan köra en sökning i loggen som returnerar alla poster genom att klicka på **se alla** längst ned på bladet eller genom att klicka på rubriken bladet.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| Kolumn | Beskrivning |
| --- | --- |
| Azure Webbappar |   |
| Trender för Web Apps begäran | Visar ett linjediagram för Web Apps begäran trend för intervallet som du har valt och visar en lista över de översta tio webbegäranden. Klicka på linjediagram om du vill köra en logg sökning efter<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> <br>Klicka på en webbserver begärandeobjekt om du vill köra en logg-sökning för web begäran mått trender som begär. |
| Svarstid för Web Apps | Visar ett linjediagram för Web Apps svarstiden för intervallet som du har valt. Dessutom visas en lista över en lista över de tio Web Apps svar gånger. Klicka på diagrammet om du vill köra en logg sökning efter<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code><br> Klicka på ett webbprogram för att köra en logg sökning returnerar svarstider för webbprogrammet. |
| Appar webbtrafik | Visar ett linjediagram för Web Apps trafik i MB och visar upp Web Apps trafik. Klicka på diagrammet om du vill köra en logg sökning efter<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code><br> Den visar alla webbprogram med trafik för den senaste minuten. Klicka på ett webbprogram för att köra en logg sökning visar antal byte som tagits emot och skickats för webbprogrammet. |
| Azure App Service-planer |   |
| App Service-planer med CPU-användning &gt; 80% | Visar det totala antalet Apptjänstplaner som har mer än 80% CPU-användning och visar en lista över de översta 10 Apptjänstplaner per CPU-användning. Klicka på området totala om du vill köra en logg sökning efter<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code><br> Den visar en lista med din App Service-planer och deras Genomsnittlig CPU-belastning. Klicka på en Apptjänstplan för att köra en logg sökning visar den genomsnittliga processoranvändningen. |
| App Service-planer med minnesanvändning &gt; 80% | Visar det totala antalet Apptjänstplaner som minnesanvändningen är större än 80% och visar en lista över de översta 10 Apptjänstplaner av minnesanvändning. Klicka på området totala om du vill köra en logg sökning efter<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code><br> Den visar en lista med din App Service-planer och deras genomsnittliga minnesanvändningen. Klicka på en Apptjänstplan för att köra en logg sökning visar den genomsnittliga minnesanvändningen. |
| Azure Web Apps aktivitetsloggar |   |
| Azure Web Apps aktivitet granskning | Visar det totala antalet Web Apps med [aktivitetsloggar](log-analytics-activity.md) och visar de översta 10 aktivitet log-funktioner. Klicka på området totala om du vill köra en logg sökning efter<code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code><br> En lista över åtgärder för aktiviteten loggen visas. Klicka på en aktivitet log-åtgärd för att köra en sökning i loggen som visar en lista över poster för åtgärden. |



### <a name="azure-web-apps"></a>Azure Web Apps

I instrumentpanelen för detaljnivån få större insyn i Web Apps-mått. Detta först uppsättning blad visa Web Apps-begäranden, antalet fel (till exempel HTTP404), trafik och genomsnittlig svarstid trend över tid. Den visar också en sammanställning av dessa mått för olika Web Apps.

![Blad för Azure Webbappar](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

En primär orsak visar att data är så att du kan identifiera en Webbapp med hög svarstid och undersöka för att hitta den bakomliggande orsaken. En tröskelvärdet används också för att hjälpa dig att enkelt identifiera de problem.

- Webbprogram som visas i rött har svarstid som är högre än 1 sekund.
- Web Apps visas med orange färg har en svarstid som är högre än 0.7 sekund och mindre än 1 sekund.
- Webbprogram som visas i grönt har en svarstid som är mindre än 0,7 andra.

I följande loggen Sök exempelbild, kan du se att den *anugup3* webbprogrammet hade en mycket högre svarstid än andra webbprogram.

![loggen Sök-exempel](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Apptjänstplaner

Om du använder dedicerade Service-planer, du kan också samla in mått för din App Service-planer. Den här vyn visas i din App Service-planer med hög CPU eller minne användning (&gt; 80%). Där visas också det högsta apptjänster med hög minne eller CPU-användning. Ett tröskelvärde gränsen tillämpas på samma sätt som hjälper dig att enkelt identifiera de med problem.

- Apptjänstplaner visas i rött har en processor/minnesanvändning högre än 80%.
- Programtjänstplaner visas med orange färg har en processor/minnesanvändning högre än 60% och lägre än 80%.
- Apptjänstplaner visas i grönt har en processor/minnesanvändning lägre än 60%.

![Blad för Azure App Service-planer](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Azure Web Apps loggen sökningar

Den **lista av populära Azure Web Apps sökfrågor** visar alla relaterade aktivitetsloggar för webbprogram som tillhandahåller insikter om de åtgärder som utfördes på resurserna i Web Apps. Dessutom visas alla relaterade åtgärder och hur många gånger som de har inträffat.

Använder loggen sökfrågor som utgångspunkt, kan du enkelt skapa en avisering. Du kanske vill skapa en avisering när genomsnittlig svarstid för ett mått som är större än 1 gång i sekunden.

## <a name="next-steps"></a>Nästa steg

- Skapa en [avisering](log-analytics-alerts-creating.md) för ett specifikt mått.
- Använd [loggen Sök](log-analytics-log-searches.md) att visa detaljerad information från din aktivitetsloggar.
