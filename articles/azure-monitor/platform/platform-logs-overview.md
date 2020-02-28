---
title: Översikt över Azures plattforms loggar | Microsoft Docs
description: Översikt över loggar i Azure Monitor som ger omfattande, frekventa data om driften av en Azure-resurs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659328"
---
# <a name="overview-of-azure-platform-logs"></a>Översikt över Azures plattforms loggar
Plattforms loggar ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. De genereras automatiskt även om du behöver konfigurera vissa plattforms loggar som ska vidarebefordras till en eller flera mål som ska behållas. Den här artikeln innehåller en översikt över plattforms loggar, inklusive vilken information de ger och hur du kan konfigurera dem för insamling och analys.

## <a name="types-of-platform-logs"></a>Typer av plattforms loggar
I följande tabell visas de olika plattforms loggar som är tillgängliga på olika lager i Azure.

| Logga | Lager | Beskrivning |
|:---|:---|:---|
| Resursloggar | Azure-resurser | Ge insikter om åtgärder som utförts i en Azure-resurs ( *data planet*), till exempel för att få en hemlighet från en Key Vault eller göra en begäran till en databas. Innehållet i resurs loggar varierar beroende på Azure-tjänsten och resurs typen.<br><br>*Resurs loggar kallades tidigare för diagnostikloggar.*  |
| Aktivitetslogg | Azure-prenumeration | Ger inblick i åtgärderna för varje Azure-resurs i prenumerationen från utsidan (*hanterings planet*) utöver uppdateringar av service Health händelser. Använd aktivitets loggen för att fastställa _vad_, _vem_och _när_ för Skriv åtgärder (skicka, skicka och ta bort) som ska vidtas för resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper.  Det finns en enda aktivitets logg för varje Azure-prenumeration. |
| Azure Active Directory loggar | Azure-klient |  Innehåller historiken för inloggnings aktivitet och gransknings spårning av ändringar som gjorts i Azure Active Directory för en viss klient. Se [Vad är Azure Active Directory rapporter?](../../active-directory/reports-monitoring/overview-reports.md) en fullständig beskrivning av Azure Active Directory loggar.   |

> [!NOTE]
> Azure aktivitets loggen är främst avsedd för aktiviteter som förekommer i Azure Resource Manager. Den spårar inte resurser med hjälp av klassisk/RDFE-modell. Vissa klassiska resurs typer har en proxy-resurs leverantör i Azure Resource Manager (till exempel Microsoft. ClassicCompute). Om du interagerar med en klassisk resurs typ via Azure Resource Manager med hjälp av dessa providers-proxyservrar, visas åtgärderna i aktivitets loggen. Om du interagerar med en klassisk resurs typ utanför Azure Resource Manager-proxyservrar registreras dina åtgärder bara i åtgärds loggen. Du kan bläddra i åtgärds loggen i ett separat avsnitt i portalen.

![Översikt över plattformsloggar](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visa plattforms loggar
Det finns olika alternativ för att visa och analysera de olika Azure-plattforms loggarna.

- Visa aktivitets loggen i Azure Portal och få åtkomst till händelser från PowerShell och CLI. Mer information finns i [Visa och hämta Azure aktivitets logg händelser](activity-log-view.md) . 
- Visa Azure Active Directory säkerhets-och aktivitets rapporter i Azure Portal. Se [Vad är Azure Active Directory rapporter?](../../active-directory/reports-monitoring/overview-reports.md)  för mer information.
- Resurs loggar skapas automatiskt av Azure-resurser som stöds, men de är inte tillgängliga för visning, om du inte skickar dem till ett [mål](#destinations). 

## <a name="destinations"></a>Bestämmelser
Du kan skicka plattforms loggar till en eller flera av målen i följande tabell, beroende på dina övervaknings krav. Konfigurera mål för plattforms loggar genom att [skapa en diagnostisk inställning](diagnostic-settings.md).

| Mål | Scenario | Referenser |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Analysera loggarna med andra övervaknings data och utnyttja Azure Monitor funktioner som logg frågor och aviseringar. | [Aktivitets logg och resurs loggar](resource-logs-collect-workspace.md)<br>[Azure aktivitets katalog loggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure-lagring | Arkivera loggarna för granskning, statisk analys eller säkerhets kopiering. |[Aktivitets logg och resurs loggar](archive-diagnostic-logs.md)<br>[Azure aktivitets katalog loggar](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Händelsehubb | Strömma loggarna till loggnings-och telemetri system från tredje part.  |[Aktivitets logg och resurs loggar](resource-logs-stream-event-hubs.md)<br>[Azure aktivitets katalog loggar](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Nästa steg

* [Visa aktivitets loggens schema för olika kategorier](activity-log-schema.md)
* [Visa resurs logg schema för olika Azure-tjänster](diagnostic-logs-schema.md)
