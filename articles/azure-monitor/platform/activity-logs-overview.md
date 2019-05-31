---
title: Översikt över Azure-aktivitetsloggen
description: Lär dig vad Azure-aktivitetsloggen är och hur du kan använda den för att förstå händelser som inträffar i din Azure-prenumeration.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245027"
---
# <a name="overview-of-azure-activity-log"></a>Översikt över Azure-aktivitetsloggen

Den **Azure-aktivitetsloggen** ger information om händelser på prenumerationsnivå som har inträffat i Azure. Detta omfattar en mängd data från Azure Resource Manager driftdata uppdateringar på Service Health-händelser. Aktivitetsloggen kallades tidigare _granskningsloggar_ eller _Driftloggar_, eftersom den administrativa kategorin rapporterar kontrollplanet händelser för dina prenumerationer. 

Använda aktivitetslogg, för att fastställa den _vad_, _som_, och _när_ för alla skrivåtgärder (PUT, POST, ta bort) vidtas på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. 

Aktivitetsloggen inkluderar inte läsåtgärder (GET) eller åtgärder för resurser som använder klassisk/RDFE-modellen.

## <a name="comparison-to-diagnostic-logs"></a>Jämförelse med diagnostikloggar
Det finns en enda aktivitetsloggen för varje Azure-prenumeration. Den innehåller data om åtgärder på en resurs från utsidan (”kontrollplanet”). [Diagnostikloggar](diagnostic-logs-overview.md) genereras av en resurs och ange information om användningen av den här resursen (”dataplanet”). Du måste aktivera diagnostikinställningar för varje resurs.

![Aktivitetsloggar jämfört med diagnostikloggar](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure-aktivitetsloggen är främst för aktiviteter som sker i Azure Resource Manager. Den spårar inte ugresources med hjälp av klassisk/RDFE-modellen. Vissa typer av klassiska resurser har en proxy-resursprovider i Azure Resource Manager (till exempel Microsoft.ClassicCompute). Om du interagerar med en klassisk resurstyp via Azure Resource Manager med hjälp av dessa proxy resursprovidrar visas åtgärderna i aktivitetsloggen. Om du interagerar med en klassisk resurstyp utanför Azure Resource Manager-proxyservrar, registreras endast dina åtgärder i loggen igen. Åtgärden loggen kan sökas i ett separat avsnitt i portalen.

## <a name="activity-log-retention"></a>Kvarhållning av logg för aktivitet
Händelser i aktivitetsloggen lagras i 90 dagar. Att lagra dessa data under längre perioder [samla in den i Azure Monitor](activity-log-collect.md) eller [exportera den till lagring eller Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Visa aktivitetsloggen
Se aktivitetsloggen för alla resurser från den **övervakaren** menyn i Azure-portalen. Se aktivitetsloggen för en viss resurs från den **aktivitetsloggen** alternativ i den resurs-menyn. Du kan också hämta aktivitetsloggen journaler med hjälp av PowerShell, CLI eller REST API.  Se [visa och hämta aktivitet för Azure logghändelser](activity-log-view.md).

![Visa aktivitetslogg](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Samla in aktivitetsloggen i Azure Monitor
Samla in aktivitetsloggen i Log Analytics-arbetsytan i Azure Monitor och analysera dem med andra övervakningsdata och vill behålla data längre än 90 dagar. Se [samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsyta i Azure Monitor](activity-log-collect.md).

![Fråga aktivitetsloggen](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportera aktivitetslogg
Exportera aktivitetsloggen till Azure Storage för arkivering eller strömma det till en Händelsehubb för inmatning av en tjänst från tredje part eller anpassade analyslösning. Se [exportera Azure-aktivitetsloggen](activity-log-export.md). Du kan också analysera aktivitetsloggen händelser i Power BI med hjälp av den [ **Power BI-Innehållspaketet**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Avisera om aktivitetsloggen
Du kan skapa en avisering när vissa händelser skapas i aktivitetsloggen med en [aktivitetsloggaviseringen](activity-log-alerts.md). Du kan också skapa en avisering med hjälp av en [loggfråga](alerts-log-query.md) när din aktivitetslogg är ansluten till en Log Analytics-arbetsyta, men det finns en kostnad för att logga fråga aviseringar. Det kostar ingenting för aviseringar för aktivitetsloggen.

## <a name="categories-in-the-activity-log"></a>Kategorier i aktivitetsloggen
Varje händelse i aktivitetsloggen har en viss kategori som beskrivs i följande tabell. Fullständig information om scheman av dessa kategorier finns i [Azure-aktivitetsloggen Händelseschema](activity-log-schema.md). 

| Category | Beskrivning |
|:---|:---|
| Administrativ | Innehåller en post för alla skapa, uppdatera och ta bort åtgärden åtgärder som utförs via Resource Manager. Exempel på administrativa händelser är _Skapa virtuell dator_ och _ta bort nätverkssäkerhetsgruppen_.<br><br>Varje åtgärd som en användare eller program med hjälp av Resource Manager modelleras som en åtgärd på en viss resurstyp. Om åtgärdstypen är _skriva_, _ta bort_, eller _åtgärd_, posterna i start- och lyckas eller misslyckas av åtgärden sparas i den administrativa kategorin. Administrativa händelser inkluderar även ändringar av rollbaserad åtkomstkontroll i en prenumeration. |
| Service Health | Innehåller en post för alla service health incidenter som har inträffat i Azure. Ett exempel på en Service Health-händelse _SQL Azure i östra USA har drabbats av driftstopp_. <br><br>Service Health-händelser levereras i fem sorterna: _Åtgärd krävs_, _Assisted Recovery_, _Incident_, _Underhåll_, _Information_, eller  _Security_. De här händelserna skapas bara om du har en resurs i den prenumeration som skulle påverkas av händelsen.
| Resource Health | Innehåller en post för eventuella resource health-händelser som har inträffat för dina Azure-resurser. Ett exempel på en händelse som Resource Health är _hälsostatus för virtuell dator ändrats till inte tillgänglig_.<br><br>Resource Health-händelser kan representera en av fyra health-status: _Tillgängliga_, _otillgänglig_, _försämrad_, och _okänd_. Dessutom Resource Health-händelser kan kategoriseras som _plattform initierade_ eller _användarinitierad_. |
| Varning | Innehåller en post för aktiveringar för Azure-aviseringar. Ett exempel på en varning avisering är _processor på myVM har varit över 80 under de senaste 5 minuterna_.|
| Automatisk skalning | Innehåller en post för alla händelser relaterade till driften av motorn för automatisk skalning baserat på alla inställningarna för automatisk skalning som du har definierat i din prenumeration. Ett exempel på en händelse med automatisk skalning är _det gick inte att utföra åtgärder för autoskalning_. |
| Rekommendation | Innehåller rekommendationshändelser från Azure Advisor. |
| Säkerhet | Innehåller en post för alla aviseringar som genereras av Azure Security Center. Ett exempel på en säkerhetshändelse är _misstänkt dubbelt filnamnstillägg fil körs_. |
| Princip | Innehåller poster för alla gälla åtgärd åtgärder som utförs av Azure Policy. Exempel på Principhändelser är _Audit_ och _neka_. Varje åtgärd som principen modelleras som en åtgärd på en resurs. |


## <a name="next-steps"></a>Nästa steg

* [Skapa en loggprofil om du vill exportera Azure-aktivitetsloggen](activity-log-export.md)
* [Stream Azure-aktivitetsloggen till Event Hubs](activity-logs-stream-event-hubs.md)
* [Arkivera Azure-aktivitetsloggen till storage](archive-activity-log.md)

