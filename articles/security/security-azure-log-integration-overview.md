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
ms.date: 08/10/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6c3a2ac18fdb7a7a722447af720b9dee28adef08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introduktion till Microsoft Azure log-integrering
Läs mer om Azure logganalys integration, de viktigaste funktionerna och hur det fungerar.

## <a name="overview"></a>Översikt

Azure logganalys-integrering är en kostnadsfri lösning som gör det möjligt att integrera loggarna från Azure-resurser i till din lokala Security Information and Event Management SIEM ()-system.

Azure logganalys integration samlar in Windows-händelser från Windows Loggboken, [Azure aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center-aviseringar](../security-center/security-center-intro.md), och [Azure diagnostiska loggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) från Azure-resurser. Den här integreringen hjälper din SIEM-lösning som ger en enhetlig instrumentpanel för alla tillgångar, lokalt eller i molnet, så att du kan aggregera korrelera, analysera och varna för säkerhetshändelser.

>[!NOTE]
För tillfället är endast stöds molnen Azure kommersiell och Azure Government. Andra moln stöds inte.

![Azure-loggintegrering][1]

## <a name="what-logs-can-i-integrate"></a>Vilka loggar kan integrera?
Azure ger utförlig loggning för varje Azure-tjänst. Dessa loggar representerar tre typer av loggar:

* **Kontroll och hantering loggar** ger insyn i den [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) skapa, uppdatera och ta bort åtgärder. Azure aktivitetsloggar är ett exempel på den här typen av loggen.
* **Data plan loggar** ger inblick i händelser som aktiverats som en del av användningen av en Azure-resurs. Ett exempel på den här typen av loggen är Windows Loggboken **System**, **säkerhet**, och **programmet** kanaler i en Windows-dator. Ett annat exempel är diagnostik loggning konfigureras via Azure-Monitor
* **Bearbetade händelser** ange analyserade händelse och aviseringsinformation bearbetas för din räkning. Ett exempel på den här typen av händelse är Azure Security Center-aviseringar, där Azure Security Center har bearbetats och analyserade din prenumeration för att tillhandahålla aviseringar som är relevanta för din aktuella säkerhetstillståndet.

Azure Log-Integration stöder ArcSight och QRadar Splunk. Kontrollera med din SIEM-leverantör för att bedöma om de har en intern koppling i samtliga fall. I vissa fall kan behöver du inte du använda Azure Log-integrering när interna kopplingar är tillgängliga. För ytterligare information om stöder loggen typer du besök vanliga frågor.

>[!NOTE]
Azure Log-integrering är en kostnadsfri lösning, finns men det kostnader för Azure storage som uppstår till följd av loggen för information fillagring.

Community-hjälp är tillgänglig via den [Azure Log Integration MSDN-Forum](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Forumet ger AzLog gemenskapen möjlighet att stödja varandra med frågor, svar, tips och råd om hur du hämtar mest av Azure Log-integrering. Dessutom övervakar det här forumet teamet Azure Log-integrering och hjälper när vi kan.

Du kan även öppna en [supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Om du vill göra det, Välj **loggen Integration** som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet berättade Azure logganalys-integration. Mer information om Azure logganalys-integrering och vilka typer av loggar som stöds finns i följande avsnitt:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center för ytterligare information, systemkrav, och installera instruktioner på Azure logganalys-integration.
* [Kom igång med Azure logganalys-integration](security-azure-log-integration-get-started.md) – den här självstudiekursen vägleder dig genom installationen av Azure logganalys-integrering och integrera loggar från Azure BOMULLSTUSS lagring, Azure aktivitetsloggar Azure Security Center-aviseringar och Azure Active Directory granskningsloggar.
* [Samarbeta konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – det här blogginlägget visar hur du konfigurerar Azure logganalys-integrering ska fungera med partnerlösningar Splunk, HP ArcSight och IBM QRadar. Den här bloggen representerar vår aktuella position om hur du konfigurerar partnerlösningar. I samtliga fall Se partner lösning dokumentationen först.
* [Aktiviteten och ASC aviseringar över syslog till QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) – det här blogginlägget innehåller instruktioner för att skicka aktiviteten och Azure Security Center aviseringar via syslog till QRadar
* [Azure logganalys Integration vanliga frågor (FAQ)](security-azure-log-integration-faq.md) – det här avsnittet får du svar frågor om Azure logganalys-integration.
* [Integrera Security Center aviseringar med Azure logga Integration](../security-center/security-center-integrating-alerts-with-log-integration.md) – det här dokumentet beskrivs hur du synkroniserar Azure Security Center-aviseringar med Azure Log-Integration.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
