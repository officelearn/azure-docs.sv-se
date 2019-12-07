---
title: Översikt över Azures plattforms loggar | Microsoft Docs
description: Översikt över loggar i Azure Monitor som ger omfattande, frekventa data om driften av en Azure-resurs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894554"
---
# <a name="overview-of-azure-platform-logs"></a>Översikt över Azures plattforms loggar
Plattforms loggar ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. De genereras automatiskt även om du behöver konfigurera vissa plattforms loggar som ska vidarebefordras till en eller flera mål som ska behållas. Den här artikeln innehåller en översikt över plattforms loggar, inklusive vilken information de ger och hur du kan konfigurera dem för insamling och analys.

## <a name="types-of-platform-logs"></a>Typer av plattforms loggar
I följande tabell visas de olika plattforms loggar som är tillgängliga på olika lager i Azure.

| Lager | Loggar | Beskrivning |
|:---|:---|:---|
| Azure-resurser | [Resurs loggar](resource-logs-overview.md) | Ge insikter om åtgärder som utförts i en Azure-resurs ( *data planet*), till exempel för att få en hemlighet från en Key Vault eller göra en begäran till en databas. Innehållet i resurs loggar varierar beroende på Azure-tjänsten och resurs typen.<br>*Resurs loggar kallades tidigare för diagnostikloggar.*  |
| Azure-prenumeration | [Aktivitetslogg](activity-logs-overview.md) | Ger inblick i åtgärderna för varje Azure-resurs i prenumerationen från utsidan (*hanterings planet*) utöver uppdateringar av service Health händelser. Det finns en enda aktivitets logg för varje Azure-prenumeration.   |
| Azure-klientorganisation | [Azure Active Directory loggar](../../active-directory/reports-monitoring/overview-reports.md)  | Innehåller historiken för inloggnings aktivitet och gransknings spårning av ändringar som gjorts i Azure Active Directory för en viss klient.   |


![Översikt över plattformsloggar](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Visa plattforms loggar
Du kan visa [aktivitets loggen](activity-log-view.md) och [Azure Active Directory loggar](../../active-directory/reports-monitoring/overview-reports.md) i Azure Portal. Du måste skicka resurs loggar till ett [mål](#destinations) om du vill visa dem.


## <a name="destinations"></a>Mål
Du kan skicka plattforms loggar till en eller flera av målen i följande tabell, beroende på dina övervaknings krav. 

| Mål | Scenario | Referenser |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Analysera loggarna med andra övervaknings data och utnyttja Azure Monitor funktioner som logg frågor och aviseringar. | [Resurs loggar](resource-logs-collect-storage.md)<br>[Aktivitetslogg](activity-log-collect.md)<br>[Azure aktivitets katalog loggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Arkivera loggarna för granskning, statisk analys eller säkerhets kopiering. |[Resurs loggar](archive-diagnostic-logs.md)<br>[Aktivitetslogg](activity-log-export.md)<br>[Azure aktivitets katalog loggar](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Händelsehubb | Strömma loggarna till loggnings-och telemetri system från tredje part.  |[Resurs loggar](resource-logs-stream-event-hubs.md)<br>[Aktivitetslogg](activity-log-export.md)<br>[Azure aktivitets katalog loggar](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Diagnostikinställningar och logg profiler
Konfigurera destinationer för resurs loggar och Azure Active Directory loggar genom att [skapa en diagnostisk inställning](diagnostic-settings.md). Konfigurera mål för aktivitets loggen genom att [skapa en logg profil](activity-log-export.md) eller genom [att ansluta den till en Log Analytics-arbetsyta](activity-log-collect.md).

Inställningen diagnostik och logg profil definierar följande:

- En eller flera destinationer för att skicka valda loggar och mått.
- Vilka logg kategorier och mått från resursen som skickas till målen.
- Om ett lagrings konto har valts som mål, hur länge varje logg kategori ska behållas.



## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitets loggen](activity-logs-overview.md)
* [Läs mer om resurs loggar](resource-logs-overview.md)
* [Visa resurs logg schema för olika Azure-tjänster](diagnostic-logs-schema.md)
