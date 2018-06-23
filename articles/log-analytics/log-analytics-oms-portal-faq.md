---
title: Vanliga frågor för övergången från OMS-portalen på Azure-portalen för Log Analytics användare | Microsoft Docs
description: Svar på vanliga frågor för Log Analytics användare övergång från OMS-portalen till Azure-portalen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: 1e0fd56b6e420103b4f786985f71a84737db642d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333640"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Vanliga frågor för övergången från OMS-portalen på Azure-portalen för logganalys-användare
Logganalys används först sin egen portalen kallas OMS-portalen för att hantera konfigurationen och analysera insamlade data.  Alla funktioner från den här portalen har flyttats till Azure portal där den fortsätter att utvecklas.

Den här artikeln innehåller vanliga frågor för användare att göra övergången.  Om du har använt logganalys i OMS-portalen kan du få svar här för hur du kan utföra samma åtgärder i Azure-portalen.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Var hittar jag logganalys i Azure?
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).  Klicka på **alla tjänster**, och Skriv i listan över resurser, **logganalys**. Välj **logganalys** och välj sedan din arbetsyta. Sammanfattningssida för arbetsytan visas.

![Log Analytics-arbetsyta](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Hur hanterar behörighet?
Om du inte har åtkomst till logganalys-arbetsytan i Azure portal, måste du konfigurera behörigheter genom [Azure rollbaserad åtkomst](../active-directory/role-based-access-control-configure.md). Mer information om hur du hanterar arbetsytebehörigheter finns [hantera arbetsytor](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Information om hur du hanterar behörighet för aviseringar finns i [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Hur skapar jag en ny arbetsyta 
I listan över arbetsytor i Azure-portalen klickar du på **Lägg till** i listan över arbetsytor.  Fullständig information finns i [skapa logganalys-arbetsytan i Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

![Översiktssidan](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Där är min översiktssida?
Huvudfönstret i OMS-portalen visar panelerna för alla hanteringslösningarna som installerats i din arbetsyta och eventuella anpassade vyer som du har skapat. Samma vyn finns tillgänglig i Azure-portalen. Från arbetsytan, Välj **arbetsytan Sammanfattning**.

![Översiktssidan](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Hur gör jag öppna loggen Sök och Vydesigner?
Båda **loggen Sök** och **Vydesigner** är tillgängliga på huvudsidan och i den vänstra menyn i din arbetsyta i Azure-portalen just nu till **översikt**.

## <a name="where-do-i-find-settings"></a>Var hittar jag inställningarna?
Många av inställningarna i den **inställningar** avsnitt i OMS-portalen är tillgängliga i den **avancerade inställningar** menyn i Azure portal för arbetsytan.

![Avancerade inställningar](media/log-analytics-new-portal/advanced-settings.png)

Följande avsnitt innehåller en fullständig lista över hur du kan komma åt inställningar som tidigare var tillgängliga i den **inställningar** avsnitt i OMS-portalen.

### <a name="accounts"></a>Konton 
Kontoinställningar hanteras på olika ställen i Azure-portalen, enligt beskrivningen i följande tabell.

| Inställningen i OMS-portalen | Motsvarande i Azure-portalen |
|:---|:---|
| Automation-konto | **Automation-konto** menyn för arbetsytan. |
| Azure-prenumeration & Dataabonnemang | **Prisnivån** menyn för arbetsytan. |
| Hantera användare | Använder Azure rollbaserad åtkomst till [hantera behörigheter för din arbetsyta](#how-do-i-manage-permissions). |
| Arbetsyteinformation | Information som är tillgängliga på **OMS-arbetsytan** menyn för arbetsytan. |

### <a name="alerts"></a>Aviseringar
Varningsregler baserat på logganalys frågor hanteras nu i den [unified aviseringar upplevelse](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Datorgrupper
Hantera datorgrupper i den **avancerade inställningar** menyn för arbetsytan. 

### <a name="connected-sources"></a>Anslutna källor
Hantera inställningar för de flesta ansluten datakälla den i den **avancerade inställningar** menyn för arbetsytan. Följande tabell innehåller information för varje avsnitt i den här menyn.

| Inställningen i OMS-portalen | Motsvarande i Azure-portalen |
|:---|:---|
| Windows-servrar   | **Avancerade inställningar** menyn för arbetsytan. |
| Linux-servrar   | **Avancerade inställningar** menyn för arbetsytan. |
| Azure Storage     | **Avancerade inställningar** menyn för arbetsytan. |
| System Center     | **Avancerade inställningar** menyn för arbetsytan. |
| Office 365        | Finns det [dokumentation för Office 365-hanteringslösning](../operations-management-suite/oms-solution-office-365.md) konfigurationsinformation. |
| Windows-telemetri | Inte tillgänglig än i Azure-portalen. |
| ITSM koppling    | Se [ansluta ITSM produkter och tjänster med IT Service Management-anslutningstjänsten](../log-analytics/log-analytics-itsmc-connections.md) anvisningar om hur du ansluter din ITSM tjänst med logganalys. |

### <a name="data"></a>Data
Hantera inställningar för de flesta Data den i den **avancerade inställningar** menyn för arbetsytan. Följande tabell innehåller information för varje avsnitt i den här menyn.

| Inställningen i OMS-portalen | Motsvarande i Azure-portalen |
|:---|:---|
| Windows-händelseloggar           | **Avancerade inställningar** menyn för arbetsytan. |
| Windows-prestandaräknare | **Avancerade inställningar** menyn för arbetsytan. |
| Linux-prestandaräknare   | **Avancerade inställningar** menyn för arbetsytan. |
| IIS-loggar                     | **Avancerade inställningar** menyn för arbetsytan. |
| Anpassade fält                | **Avancerade inställningar** menyn för arbetsytan. |
| Anpassade loggar                  | **Avancerade inställningar** menyn för arbetsytan. |
| Syslog                       | **Avancerade inställningar** menyn för arbetsytan. |
| Application Insights         | Den här lösningen är föråldrad nu när logganalys och Application Insights delar samma datamotor för.  |
| Filspårning i Windows        | **Ändringsspårning** -menyn i Azure Automation. Se [spåra ändringar i din miljö med lösningen för ändringsspårning](../automation/automation-change-tracking.md) mer information. |
| Registerspårning i Windows        | **Ändringsspårning** -menyn i Azure Automation. Se [spåra ändringar i din miljö med lösningen för ändringsspårning](../automation/automation-change-tracking.md) mer information. |
| Filspårning i Linux          | **Ändringsspårning** -menyn i Azure Automation. Se [spåra ändringar i din miljö med lösningen för ändringsspårning](../automation/automation-change-tracking.md) mer information. |

### <a name="solutions"></a>Lösningar
Hantera lösningar i den **lösningar** menyn för arbetsytan. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Hur jag för att installera och ta bort hanteringslösningar?
I OMS-portalen installerar hanteringslösningar från galleriet lösningar och ta bort dem från **inställningar**. I Azure-portalen [installera hanteringslösningar](../monitoring/monitoring-solutions.md#install-a-management-solution) från Azure Marketplace. [Ta bort lösningar](../monitoring/monitoring-solutions.md#remove-a-management-solution) från listan över installerade lösningar.

## <a name="how-do-i-create-and-manage-alerts"></a>Hur jag för att skapa och hantera aviseringar?
Varningsregler baserat på logganalys frågor hanteras nu i den [unified aviseringar upplevelse](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Se [hur du utökar aviseringar från Log Analytics till Azure aviseringar](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) mer information om hur du konfigurerar och använder aviseringar i Azure-portalen.

## <a name="how-do-i-access-my-dashboards"></a>Hur kommer jag åt mina instrumentpaneler?
[Instrumentpaneler](../log-analytics/log-analytics-dashboards.md) i logganalys har ersatts.  Du kan visa data i Log Analytics med hjälp av [Vydesigner](../log-analytics/log-analytics-view-designer.md) som har ytterligare funktioner och pin-fråga och vyer till Azure instrumentpaneler.

## <a name="how-do-i-check-my-usage"></a>Hur jag för att kontrollera min användning?
Du kan nu enkelt visa och hantera din användning och kostnader för Log Analytics genom att välja **användning och de uppskattade kostnaderna** på arbetsytan.

![Användning och uppskattade kostnader](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Kan jag fortsätta att använda den klassiska portalen?
Under en begränsad tid, du kan fortfarande åtkomst till portalen via denna URL med arbetsytans namn: https://\<arbetsytans namn\>. portal.mms.microsoft.com. Vi rekommenderar att du använder Azure-portalen men och ge oss feedback på LAUpgradeFeedback@microsoft.com på eventuella blockeringsproblem.

## <a name="next-steps"></a>Nästa steg

- [Hitta och installera hanteringslösningar](../monitoring/monitoring-solutions.md) med Azure-portalen.
- Lär dig mer om [loggen Sök i Azure portal](log-analytics-log-search-portals.md).