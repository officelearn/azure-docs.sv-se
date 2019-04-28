---
title: Växla från äldre Log Analytics aviseringar API till nytt API för Azure-aviseringar
description: 'Översikt över tillbakadragning av äldre savedSearch-baserad API: et för Log Analytics-avisering och processen för att växla Varningsregler till den nya ScheduledQueryRules API, med information om vanliga kundernas synpunkter-adressering.'
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1706fc050fecd2e4be3a40725ec3e63a9036b3a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996038"
---
# <a name="switch-api-preference-for-log-alerts"></a>Växla API till inställningar för Loggaviseringar

> [!NOTE]
> Innehåll som anges gäller för användare endast Azure offentligt moln och **inte** för Azure Government eller Azure Kina-molnet.  

Tills nyligen hanterade Varningsregler i Microsoft Operations Management Suite-portalen. Det nya aviseringsgränssnittet har integrerats med ett antal tjänster i Microsoft Azure, inklusive Log Analytics och vi ombeds [utöka notifieringsregler från OMS-portalen till Azure](alerts-extend.md). Men för att säkerställa störningar för kunder, processen inte att ändra programmeringsgränssnitt för dess användning - [Log Analytics-avisering API](api-alerts.md) baserat på SavedSearch.

Men nu meddela för Log Analytics-avisering användare ett sant Azure programmässiga alternativ, [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), som också är reflekterande i din [Azure-fakturering – för loggaviseringar](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Mer information om hur du hanterar dina aviseringar med hjälp av API: et finns [Hantera aviseringar med hjälp av Azure-resursmall](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [Hantera aviseringar med PowerShell, CLI eller API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Fördelarna med att växla till nya Azure-API

Det finns flera fördelar med att skapa och hantera aviseringar med hjälp av [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) över [äldre Log Analytics avisering API](api-alerts.md); vi har angett några av de största nedan:

- Möjligheten att [mellan arbetsytan loggsökning](../log-query/cross-workspace-query.md) i Varningsregler och span in externa resurser som Log Analytics-arbetsytor eller även Application Insights-appar
- När flera fält används för att gruppen i fråga, med hjälp av [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) användaren kan ange vilka fält som ska aggregering på Azure-portalen
- Loggaviseringar som skapats med hjälp av [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan period definierat upp till 48 timmar och av hämtningsdata under längre tid än tidigare
- Skapa aviseringsregler i en bild som en enskild resurs utan att behöva skapa tre nivåer av resurser som med [äldre Log Analytics avisering API](api-alerts.md)
- Enkel programmeringsgränssnitt för alla varianter av frågebaserade aviseringar i Azure – nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan användas för att hantera regler för Log Analytics som Application Insights
- Alla nya logga avisering funktioner och framtida utveckling blir tillgängliga via den nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processen för att växla från äldre Log aviseringar API

Processen att flytta Varningsregler från [äldre Log Analytics avisering API](api-alerts.md) inbegriper inte ändra aviseringens definition, fråga eller konfiguration på något sätt. Dina aviseringsregler och övervakning är påverkas inte och aviseringarna kommer inte att stoppa eller ha stoppats, under eller efter växeln.

Användare kan använda antingen [äldre Log Analytics avisering API](api-alerts.md) eller nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Aviseringsregler som skapats av antingen API, kommer *hanteras av samma API* – och som från Azure-portalen. Som standard Azure Monitor kommer att fortsätta att använda [äldre Log Analytics avisering API](api-alerts.md) för att skapa någon ny aviseringsregel från Azure-portalen.

Påverkan av växeln inställning till scheduledQueryRules API kompileras nedan:

- Alla interaktioner som klar för hantering av aviseringar via programgränssnitt måste nu utföras med hjälp av [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i stället. Mer information finns i, [exemplet används, via Azure-resursmall](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [exemplet används, via Azure CLI och PowerShell](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Alla nya loggvarningsregel som skapats i Azure-portalen kommer att skapas med [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) endast och att användare använder den [ytterligare funktioner för nya API: et](#benefits-of-switching-to-new-azure-api) via Azure portal samt
- Allvarlighetsgrad för loggaviseringsregler växlar från: *Kritisk, varning och information*till *allvarlighetsgrader 0, 1 och 2*. Tillsammans med alternativet för att skapa/uppdatera Varningsregler med allvarlighetsgrad 4 samt.

> [!CAUTION]
> När en användare väljer om du vill byta inställning till den nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), regler kan inte välja tillbaka eller återgå till användning av den äldre [äldre Log Analytics avisering API](api-alerts.md).

Alla kunder som vill växla frivilligt till den nya [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och blockera användning från den [äldre Log Analytics avisering API](api-alerts.md); kan göra det genom att utföra en PUT-anrop på den nedan API för att växla alla avisering regler som är associerade med specifika Log Analytics-arbetsytan.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Med begäran brödtext som innehåller den nedan JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

API: et kan även nås från en PowerShell-kommandoraden med [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Som på bilden nedan i exemplet PUT-anrop med ARMclient verktyget för att växla alla aviseringsregler som är associerade med specifika Log Analytics-arbetsytan.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Om växeln för alla Varningsregler i Log Analytics-arbetsytan att använda nya [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) är lyckas följande svar visas.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Användarna kan också kontrollera aktuell status för Log Analytics-arbetsytan och se om det har eller inte har växlats om du vill använda [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) endast. Du kan kontrollera genom att användarna kan utföra en GET-anrop för den nedan API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Att köra ovanstående i med hjälp av PowerShell från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient) verktyget, se exemplet nedan.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om den angivna Log Analytics-arbetsytan har växlats till använda [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) svaret JSON blir enligt nedan.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Annat, om den angivna Log Analytics-arbetsytan inte är ännu nu om du vill använda [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) svaret JSON blir enligt nedan.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [Azure Monitor - aviseringar](alerts-unified-log.md).
- Lär dig hur du skapar [loggaviseringar i Azure Alerts](alerts-log.md).
- Läs mer om den [Azure Alerts uppleva](../../azure-monitor/platform/alerts-overview.md).
