---
title: Översikt över Azure-plattformsloggar | Microsoft-dokument
description: Översikt över loggar i Azure Monitor som ger omfattande, frekventa data om driften av en Azure-resurs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659328"
---
# <a name="overview-of-azure-platform-logs"></a>Översikt över Azure-plattformsloggar
Plattformsloggar ger detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform de är beroende av. De genereras automatiskt även om du måste konfigurera vissa plattformsloggar som ska vidarebefordras till en eller flera destinationer som ska behållas. Den här artikeln innehåller en översikt över plattformsloggar, inklusive vilken information de tillhandahåller och hur du kan konfigurera dem för insamling och analys.

## <a name="types-of-platform-logs"></a>Typer av plattformsloggar
I följande tabell visas de specifika plattformsloggar som är tillgängliga på olika lager av Azure.

| Logga | Lager | Beskrivning |
|:---|:---|:---|
| Resursloggar | Azure-resurser | Ge insikt i åtgärder som utfördes inom en Azure-resurs *(dataplanet),* till exempel att få en hemlighet från ett Key Vault eller göra en begäran till en databas. Innehållet i resursloggar varierar beroende på Azure-tjänst och resurstyp.<br><br>*Resursloggar kallades tidigare diagnostikloggar.*  |
| Aktivitetslogg | Azure-prenumeration | Ger insikt i åtgärderna på varje Azure-resurs i prenumerationen utifrån (*hanteringsplanet*) förutom uppdateringar om Service Health-händelser. Använd aktivitetsloggen för att bestämma _vad_, _vem_och _när_ för alla skrivåtgärder (PUT, POST, DELETE) som tagits på resurserna i prenumerationen. Du kan också förstå status för åtgärden och andra relevanta egenskaper.  Det finns en enda aktivitetslogg för varje Azure-prenumeration. |
| Azure Active Directory-loggar | Azure-klientorganisation |  Innehåller historiken för inloggningsaktivitet och granskningsspårning av ändringar som gjorts i Azure Active Directory för en viss klient. Se [Vad är Azure Active Directory-rapporter?](../../active-directory/reports-monitoring/overview-reports.md)   |

> [!NOTE]
> Azure-aktivitetsloggen är främst för aktiviteter som inträffar i Azure Resource Manager. Den spårar inte resurser med modellen Classic/RDFE. Vissa klassiska resurstyper har en proxyresursleverantör i Azure Resource Manager (till exempel Microsoft.ClassicCompute). Om du interagerar med en klassisk resurstyp via Azure Resource Manager med hjälp av dessa proxyresursleverantörer visas åtgärderna i aktivitetsloggen. Om du interagerar med en klassisk resurstyp utanför Azure Resource Manager-proxyservrar registreras dina åtgärder bara i åtgärdsloggen. Åtgärdsloggen kan bläddras i ett separat avsnitt av portalen.

![Översikt över plattformsloggar](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visa plattformsloggar
Det finns olika alternativ för att visa och analysera de olika Azure-plattformsloggarna.

- Visa aktivitetsloggen i Azure-portalen och få åtkomst till händelser från PowerShell och CLI. Mer information finns i [Visa och hämta Azure Activity-logghändelser.](activity-log-view.md) 
- Visa Azure Active Directory Security and Activity-rapporter i Azure-portalen. Se [Vad är Azure Active Directory-rapporter?](../../active-directory/reports-monitoring/overview-reports.md)  för mer information.
- Resursloggar genereras automatiskt av Azure-resurser som stöds, men de är inte tillgängliga för visning om du inte skickar dem till ett [mål](#destinations). 

## <a name="destinations"></a>Mål
Du kan skicka plattformsloggar till en eller flera av destinationerna i följande tabell beroende på dina övervakningskrav. Konfigurera mål för plattformsloggar genom [att skapa en diagnostikinställning](diagnostic-settings.md).

| Mål | Scenario | Referenser |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Analysera loggarna med andra övervakningsdata och utnyttja Azure Monitor-funktioner som loggfrågor och aviseringar. | [Aktivitetslogg och resursloggar](resource-logs-collect-workspace.md)<br>[Azure-aktivitetskatalogloggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Arkivera loggarna för granskning, statisk analys eller säkerhetskopiering. |[Aktivitetslogg och resursloggar](archive-diagnostic-logs.md)<br>[Azure-aktivitetskatalogloggar](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Händelsehubb | Strömma loggarna till loggnings- och telemetrisystem från tredje part.  |[Aktivitetslogg och resursloggar](resource-logs-stream-event-hubs.md)<br>[Azure-aktivitetskatalogloggar](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Nästa steg

* [Visa aktivitetsloggschemat för olika kategorier](activity-log-schema.md)
* [Visa resursloggschemat för olika Azure-tjänster](diagnostic-logs-schema.md)
