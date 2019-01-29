---
title: Växla från äldre Log Analytics aviseringar API till nytt API för Azure-aviseringar
description: 'Översikt över tillbakadragning av äldre savedSearch-baserad API: et för Log Analytics-avisering och processen för att växla Varningsregler till den nya ScheduledQueryRules API, med information om vanliga kundernas synpunkter-adressering.'
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 8e123705443c44373734a81936459a56bbb259cc
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55106043"
---
# <a name="switch-api-preference-for-log-alerts"></a>Växla API till inställningar för Loggaviseringar

> [!NOTE]
> Innehåll som anges **inte** gäller för användare av Azure GovCloud och endast på användare offentliga Azure-molnet.  

Tills nyligen hanterade Varningsregler i Microsoft Operations Management Suite-portalen. Det nya aviseringsgränssnittet har integrerats med ett antal tjänster i Microsoft Azure, inklusive Log Analytics och vi ombeds [utöka notifieringsregler från OMS-portalen till Azure](alerts-extend.md). Men för att säkerställa störningar för kunder, processen inte att ändra programmeringsgränssnitt för dess användning - [Log Analytics-avisering API](api-alerts.md) baserat på SavedSearch.

Men nu meddela för Log Analytics-avisering användare ett sant Azure programmässiga alternativ, [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), som också är reflekterande i din [Azure-fakturering – för loggaviseringar](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Mer information om hur du hanterar dina aviseringar med hjälp av API: et finns [Hantera aviseringar med hjälp av Azure-resursmall](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [Hantera aviseringar med PowerShell, CLI eller API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Fördelarna med att växla till nya Azure-API

Det finns flera fördelar med att skapa och hantera aviseringar med hjälp av [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) över [äldre Log Analytics avisering API](api-alerts.md); vi har angett några av de största nedan:

- Möjligheten att [mellan arbetsytan loggsökning](../log-query/cross-workspace-query.md) i Varningsregler och span in externa resurser som Log Analytics-arbetsytor eller även Application Insights-appar
- När flera fält används för att gruppen i fråga, med hjälp av [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) användaren kan ange vilka fält som ska aggregering på Azure-portalen
- Skapa aviseringsregler i en bild som en enskild resurs utan att behöva skapa tre nivåer av resurser som med [äldre Log Analytics avisering API](api-alerts.md)
- Enkel programmeringsgränssnitt för alla varianter av frågebaserade aviseringar i Azure – nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan användas för att hantera regler för Log Analytics som Application Insights
- Alla nya logga avisering funktioner och framtida utveckling blir tillgängliga via den nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processen för att växla från äldre Log aviseringar API

Processen att flytta Varningsregler från [äldre Log Analytics avisering API](api-alerts.md) inbegriper inte ändra aviseringens definition, fråga eller konfiguration på något sätt. Användare kan använda antingen [äldre Log Analytics avisering API](api-alerts.md) eller nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Aviseringsregler som skapats av antingen API, kommer *hanteras av samma API* – och som från Azure-portalen. Som standard Azure Monitor kommer att fortsätta att använda [äldre Log Analytics avisering API](api-alerts.md) för att skapa någon ny aviseringsregel från Azure-portalen.

Påverkan av växeln inställning till scheduledQueryRules API kompileras nedan:

- Alla interaktioner som klar för hantering av aviseringar via programgränssnitt måste nu utföras med hjälp av [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i stället. Mer information finns i, [exemplet används, via Azure-resursmall](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [exemplet används, via Azure CLI och PowerShell](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Alla nya loggvarningsregel som skapats i Azure-portalen kommer att skapas med [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) endast och att användare använder den [ytterligare funktioner för nya API: et](#Benefits-of-switching-to-new-Azure-API) via Azure portal samt

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
