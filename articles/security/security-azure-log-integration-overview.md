---
title: "Integrera loggar från Azure-resurser i din SIEM-system | Microsoft Docs"
description: "Läs mer om Azure logganalys integration, de viktigaste funktionerna och hur det fungerar."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introduktion till Microsoft Azure log-integrering

Azure logganalys-integrering gör det möjligt att integrera loggarna från Azure-resurser med dina lokala Security Information and Event Management SIEM ()-system i händelse av att en koppling till [Azure-Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) ännu inte är tillgängliga från din SIEM-leverantör.

Den bästa metoden för att integrera Azure loggar är med hjälp av leverantören SIEM Azure-Monitor koppling och följande [instruktioner](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Men om din SIEM-leverantör inte ger en koppling till Azure-Monitor, du kan använda Azure Log-integrering som en tillfällig lösning (om din SIEM stöds av Azure Log-integrering) tills en sådan anslutning är tillgänglig.

>[!IMPORTANT]
>Om ditt primära intresse finns i insamling av loggar för virtuell dator, även de flesta SIEM-leverantörer detta i sitt lösning. Med SIEM ska leverantörens kopplingen alltid vara det bästa alternativet.

Azure logganalys integration samlar in Windows-händelser från Windows Loggboken, [Azure aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center-aviseringar](../security-center/security-center-intro.md), och [Azure diagnostiska loggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) från Azure-resurser. Den här integreringen hjälper din SIEM-lösning som ger en enhetlig instrumentpanel för alla tillgångar, lokalt eller i molnet, så att du kan aggregera korrelera, analysera och varna för säkerhetshändelser.

>[!NOTE]
För tillfället är endast stöds molnen Azure kommersiell och Azure Government. Andra moln stöds inte.

![Azure-loggintegrering][1]

## <a name="what-logs-can-i-integrate"></a>Vilka loggar kan integrera?

Azure ger utförlig loggning för varje Azure-tjänst. Dessa loggar representerar tre typer av loggar:

* **Kontroll och hantering loggar** ger insyn i den [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) skapa, uppdatera och ta bort åtgärder. Azure aktivitetsloggar är ett exempel på den här typen av loggen.
* **Data plan loggar** ger inblick i händelser som aktiverats som en del av användningen av en Azure-resurs. Ett exempel på den här typen av loggen är Windows Loggboken **System**, **säkerhet**, och **programmet** kanaler i en Windows-dator. Ett annat exempel är diagnostik loggning konfigureras via Azure-Monitor
* **Bearbetade händelser** ange analyserade händelse och aviseringsinformation bearbetas för din räkning. Ett exempel på den här typen av händelse är Azure Security Center-aviseringar, där Azure Security Center har bearbetats och analyserade din prenumeration för att tillhandahålla aviseringar som är relevanta för din aktuella säkerhetstillståndet.

Azure Log-Integration stöder ArcSight och QRadar Splunk. Kontrollera med leverantören SIEM för att bedöma om de har en intern koppling i samtliga fall. Du bör inte använda Azure Log-integrering när interna kopplingar är tillgängliga.

Om det inte finns några andra alternativ anses Azure Log-integrering. Följande tabell innehåller våra rekommendationer.

|**SIEM** | **Kunder som redan använder loggen integrator** | **Kunden undersöka integreringsalternativen för SIEM**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | Börja migrera till den [Azure-monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/) | Använd [SPLUNK koppling](https://splunkbase.splunk.com/app/3534/) |
|**IBM QRADAR** | Migrera till eller börjar använda QRadar kopplingen dokumenteras i slutet av http://aka.ms/azmoneventhub | Använda anslutningstjänsten QRadar dokumenteras i slutet av http://aka.ms/azmoneventhub  |
|**ARCSIGHT** | Fortsätta att använda Log Integrator förrän en anslutning är tillgänglig och sedan migrera till connector-baserad lösning.  | Överväg att Azure logganalys som ett alternativ. Gör inte publicera till Azure Log-integrering om du är beredd att gå igenom migreringsprocessen när anslutningen blir tillgänglig. |

>[!NOTE]
>Azure Log-integrering är en kostnadsfri lösning, finns men det kostnader för Azure storage som uppstår till följd av loggen för information fillagring.

Om du behöver hjälp kan du öppna en [supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Om du vill göra det, Välj **loggen Integration** som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet berättade Azure logganalys-integration. Mer information om Azure logganalys-integrering och vilka typer av loggar som stöds finns i följande avsnitt:

* [Kom igång med Azure logganalys-integration](security-azure-log-integration-get-started.md) – den här självstudiekursen vägleder dig genom installationen av Azure logganalys-integrering och integrera loggar från Azure BOMULLSTUSS lagring, Azure aktivitetsloggar Azure Security Center-aviseringar och Azure Active Directory granskningsloggar.
* [Azure logganalys Integration vanliga frågor (FAQ)](security-azure-log-integration-faq.md) – det här avsnittet får du svar frågor om Azure logganalys-integration.
* [Dataströmmen Azure övervakningsdata till en händelsehubb för användning av ett externt verktyg](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
