---
title: Vanliga frågor för övergången från OMS-portalen till Azure-portalen för Log Analytics användare | Microsoft Docs
description: Svar på vanliga frågor för Log Analytics-användare övergång från OMS-portalen till Azure-portalen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 9aa90c5a99da70aa0803aad4894e928a422b8fcf
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712253"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Vanliga frågor för övergången från OMS-portalen till Azure-portalen för Log Analytics-användare
Log Analytics används först en egen portal kallas OMS-portalen för att hantera konfigurationen och analysera insamlade data.  Alla funktioner från den här portalen har flyttats till Azure-portalen där den kommer att fortsätta att utvecklas.

Den här artikeln innehåller vanliga frågor och svar för användare som skapar den här ändringen.  Om du har använt Log Analytics i OMS-portalen kan du få svar här för hur du kan utföra samma uppgifter i Azure-portalen.

## <a name="do-i-need-to-migrate-anything"></a>Behöver jag migrera något?
Nej. Det finns inga ändringar som görs till Log Analytics, så det finns inget som behöver migreras. Det enda som ändras är det gränssnitt som du använder för att komma åt den. I själva verket kan du använda Azure portal för att komma åt samma arbetsytor, lösningar, vyer och loggsökningar som du använder i OMS-portalen redan idag.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Var hittar jag Log Analytics i Azure?
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).  Klicka på **alla tjänster**, och i listan över resurser skriver **Log Analytics**. Välj **Log Analytics** och välj sedan din arbetsyta. Sammanfattningssida för arbetsytan visas.

![Log Analytics-arbetsyta](media/log-analytics-oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Hur gör jag för att hantera behörigheter?
Om du inte har åtkomst till Log Analytics-arbetsytan i Azure-portalen kan du behöva konfigurera behörigheter genom [Azure rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md). Mer information om hur du hanterar behörigheter för arbetsytan finns i [hantera arbetsytor](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Information om hur du hanterar behörigheter för aviseringar finns i [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Hur gör jag för att skapa en ny arbetsyta? 
I listan över arbetsytor i Azure-portalen klickar du på **Lägg till** i listan över arbetsytor.  Fullständig information finns i [skapa en Log Analytics-arbetsyta i Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).

![Översiktssidan](media/log-analytics-oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Var finns min översiktssidan?
Huvudskärmen i OMS-portalen visar panelerna för alla hanteringslösningar som är installerade i din arbetsyta och eventuella anpassade vyer som du har skapat. Det här samma vy finns i Azure-portalen. Från arbetsytan, Välj **arbetsytan Sammanfattning**.

![Översiktssidan](media/log-analytics-oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Hur öppnar jag Loggsökning och Vydesigner?
Båda **Loggsökning** och **Vydesigner** är tillgängliga på huvudsidan och på den vänstra menyn på din arbetsyta i Azure-portalen just bredvid till **översikt**.

## <a name="where-do-i-find-settings"></a>Var hittar jag inställningarna?
Många av inställningarna i den **inställningar** i OMS-portalen är tillgängliga i den **avancerade inställningar** menyn i Azure portal för arbetsytan.

![Avancerade inställningar](media/log-analytics-oms-portal-faq/advanced-settings.png)

Följande avsnitt innehåller en fullständig lista över hur du kan komma åt inställningar som tidigare var tillgängliga i den **inställningar** i OMS-portalen.

### <a name="accounts"></a>Konton 
Kontoinställningar hanteras på olika ställen i Azure portal enligt beskrivningen i följande tabell.

| Inställningen i OMS-portalen | Motsvarande i Azure portal |
|:---|:---|
| Automation-konto | **Automation-konto** menyn för arbetsytan. |
| Azure-prenumeration och dataplan | **Prisnivå** menyn för arbetsytan. |
| Hantera användare | Använd Azure rollbaserad åtkomst till [hantera behörigheter för arbetsytan](#how-do-i-manage-permissions). |
| Arbetsyteinformation | Information som är tillgängliga på **OMS-arbetsyta** menyn för arbetsytan. |

### <a name="alerts"></a>Aviseringar
Varningsregler baserat på Log Analytics-frågor hanteras nu i den [unified avisering upplevelse](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Datorgrupper
Hantera datorgrupper i den **avancerade inställningar** menyn för arbetsytan. 

### <a name="connected-sources"></a>Anslutna källor
Hantera inställningar för de flesta ansluten datakälla den i den **avancerade inställningar** menyn för arbetsytan. I följande tabell innehåller information för varje avsnitt i den här menyn.

| Inställningen i OMS-portalen | Motsvarande i Azure portal |
|:---|:---|
| Windows-servrar   | **Avancerade inställningar** menyn för arbetsytan. |
| Linux-servrar   | **Avancerade inställningar** menyn för arbetsytan. |
| Azure Storage     | **Avancerade inställningar** menyn för arbetsytan. |
| System Center     | **Avancerade inställningar** menyn för arbetsytan. |
| Office 365        | Se den [dokumentation för Office 365 hanteringslösning](../azure-monitor/insights/solution-office-365.md) konfigurationsinformation. |
| Windows-telemetri | Inställningsmenyn för lösningen. Se [Windows Analytics i Azure-portalen](/windows/deployment/update/windows-analytics-azure-portal) konfigurationsinformation. |
| ITSM-anslutningsprogram    | Se [ansluta ITSM-produkter/tjänster med IT Service Management Connector](../log-analytics/log-analytics-itsmc-connections.md) anvisningar om hur du ansluter din ITSM-tjänst med Log Analytics. |

### <a name="data"></a>Data
Hantera inställningar för de flesta den i den **avancerade inställningar** menyn för arbetsytan. I följande tabell innehåller information för varje avsnitt i den här menyn.

| Inställningen i OMS-portalen | Motsvarande i Azure portal |
|:---|:---|
| Windows-händelseloggar           | **Avancerade inställningar** menyn för arbetsytan. |
| Windows-prestandaräknare | **Avancerade inställningar** menyn för arbetsytan. |
| Prestandaräknare för Linux   | **Avancerade inställningar** menyn för arbetsytan. |
| IIS-loggar                     | **Avancerade inställningar** menyn för arbetsytan. |
| Anpassade fält                | **Avancerade inställningar** menyn för arbetsytan. |
| Anpassade loggar                  | **Avancerade inställningar** menyn för arbetsytan. |
| Syslog                       | **Avancerade inställningar** menyn för arbetsytan. |
| Application Insights         | Den här lösningen är inaktuell nu när Log Analytics och Application Insights delar samma datamotor för.  |
| Filspårning i Windows        | **Ändringsspårning** menyn i Azure Automation. Se [spåra ändringar i miljön med lösningen ändringsspårning](../automation/automation-change-tracking.md) mer information. |
| Registerspårning i Windows        | **Ändringsspårning** menyn i Azure Automation. Se [spåra ändringar i miljön med lösningen ändringsspårning](../automation/automation-change-tracking.md) mer information. |
| Filspårning i Linux          | **Ändringsspårning** menyn i Azure Automation. Se [spåra ändringar i miljön med lösningen ändringsspårning](../automation/automation-change-tracking.md) mer information. |

### <a name="solutions"></a>Lösningar
Hantera lösningar i den **lösningar** menyn för arbetsytan. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Hur jag för att installera och ta bort hanteringslösningar?
I OMS-portalen kan du installera lösningar från lösningsgalleriet och bort dem från **inställningar**. I Azure-portalen [installera hanteringslösningar](../azure-monitor/insights/solutions.md#install-a-management-solution) från Azure Marketplace. [Ta bort lösningar](../azure-monitor/insights/solutions.md#remove-a-management-solution) i listan över installerade lösningar.

## <a name="how-do-i-create-and-manage-alerts"></a>Hur jag för att skapa och hantera aviseringar?
Varningsregler baserat på Log Analytics-frågor hanteras nu i den [unified avisering upplevelse](../monitoring-and-diagnostics/alert-metric.md). Se [utöka aviseringar från Log Analytics i Azure Alerts](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) för information om hur du konfigurerar och använder aviseringar i Azure-portalen.

## <a name="how-do-i-access-my-dashboards"></a>Hur kommer jag åt mina instrumentpaneler?
Den [min instrumentpanel](../log-analytics/log-analytics-dashboards.md) funktionen i Log Analytics är inaktuell. Den här funktionen har tillåtit dig att ha en privat samling av Vydesigner delar och ersätts av den inbyggda funktionen för Azure-instrumentpanelen. Du kan fortsätta att visualisera data i Log Analytics med hjälp av [Vydesigner](../log-analytics/log-analytics-view-designer.md) för delade vyer. Du kan även fästa visualiseringar från dessa vyer eller [enskilda frågor](../log-analytics/log-analytics-tutorial-dashboards.md) till Azure-instrumentpaneler.

## <a name="how-do-i-check-my-usage"></a>Hur kontrollerar jag min användning?
Du kan nu enkelt visa och hantera din användning och kostnader för Log Analytics genom att välja **användning och uppskattade kostnader** i din arbetsyta.

![Användning och uppskattade kostnader](media/log-analytics-oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Kan jag fortfarande använda den klassiska portalen?
Under en begränsad tid kan du fortfarande använda portalen via denna URL med Arbetsytenamn på din: https://\<ditt Arbetsytenamn\>. portal.mms.microsoft.com. Vi rekommenderar att du använder Azure-portalen men och ge oss feedback på LAUpgradeFeedback@microsoft.com på eventuella blockeringsproblem.

## <a name="next-steps"></a>Nästa steg

- [Hitta och installera lösningar för hantering av](../azure-monitor/insights/solutions.md) med Azure portal.
- Lär dig mer om [Loggsökning i Azure-portalen](log-analytics-log-search-portals.md).
