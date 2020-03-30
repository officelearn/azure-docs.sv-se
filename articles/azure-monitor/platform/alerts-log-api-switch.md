---
title: Växla från äldre API för Logganalysvarningar till nytt Azure Alerts API
description: Översikt över äldre sparadeSökbaserat Log Analytics Alert API och process för att växla varningsregler till nya ScheduledQueryRules API, med information om vanliga kundproblem.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249443"
---
# <a name="switch-api-preference-for-log-alerts"></a>Växla API-inställningar för loggaviseringar

> [!NOTE]
> Innehåll som anges gäller för användare Azure offentliga molnet bara och **inte** för Azure Government eller Azure Kina moln.  

> [!NOTE]
> När en användare väljer att byta inställning till det nya [schemalagdaQueryRules API:et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) är det inte möjligt att återgå till att använda det äldre [äldre Log Analytics Alert API.](api-alerts.md)

Tills nyligen hanterade du varningsregler i Microsoft Operations Management Suite-portalen. Den nya aviseringsupplevelsen integrerades med olika tjänster i Microsoft Azure, inklusive Log Analytics och vi bad att [utöka dina varningsregler från OMS-portalen till Azure](alerts-extend.md). Men för att säkerställa minimala störningar för kunder, ändrade processen inte det programmatiska gränssnittet för sin förbrukning - [Log Analytics Alert API](api-alerts.md) baserat på SavedSearch.

Men nu meddelar du för Log Analytics att varna användare ett riktigt Azure-programmatiskt alternativ, [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), som också återspeglar i din [Azure-fakturering - för loggaviseringar](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Mer information om hur du hanterar dina loggaviseringar med API:et finns i [Hantera loggaviseringar med Hjälp av Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) och Hantera [loggaviseringar med PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Fördelar med att byta till nytt Azure API

Det finns flera fördelar med att skapa och hantera aviseringar med [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) via [äldre Log Analytics Alert API;](api-alerts.md) vi har listat några av de stora nedan:

- Möjlighet att [korsa arbetsyteloggsökning](../log-query/cross-workspace-query.md) i varningsregler och sträcka ut externa resurser som Log Analytics-arbetsytor eller till och med Application Insights-appar
- När flera fält som används för att gruppera i fråga kan användare med [schemalagdaqueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ange vilket fält som ska läggas på i Azure-portalen
- Loggaviseringar som skapats med [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan ha period definierat upp till 48 timmar och hämta data under längre period än tidigare
- Skapa varningsregler i ett slag som en enda resurs utan att behöva skapa tre resursnivåer som med [äldre Log Analytics Alert API](api-alerts.md)
- Ett programmatiskt gränssnitt för alla varianter av frågebaserade loggaviseringar i Azure – nytt [schemalagtQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan användas för att hantera regler för Logganalys samt Application Insights
- Hantera loggaviseringar med [Powershell-cmdlets](alerts-log.md#managing-log-alerts-using-powershell)
- Alla nya loggvarningsfunktioner och framtida utveckling kommer endast att vara tillgängliga via det nya [schemalagdaQueryRules API:et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Process för att byta från äldre API för loggvarningar

Användare kan använda antingen [äldre Log Analytics Alert API](api-alerts.md) eller det nya [schemalagdaQueryRules API.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Varningsregler som skapas av antingen API, kommer att *vara hanterbara av samma API bara* - liksom från Azure-portalen. Som standard fortsätter Azure Monitor att använda [äldre Log Analytics Alert API](api-alerts.md) för att skapa en ny aviseringsregel från Azure-portalen för befintliga arbetsytor i Logganalys. Som [aviserats nya Loggarbetsyta som skapats på eller efter den 1 juni 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) - kommer automatiskt att använda nya [schemalagdaQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) som standard, inklusive i Azure-portalen.

Effekterna av övergången av preferens till scheduledQueryRules API sammanställs nedan:

- Alla interaktioner som görs för att hantera loggvarningar via programmatiska gränssnitt måste nu göras med [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) istället. Mer information finns i [exempelanvändning via Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [exempelanvändning via PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Alla nya loggaviseringsregel som skapas i Azure-portalen skapas endast med [schemalagdaQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och tillåter användare att använda [ytterligare funktioner i nytt API](#benefits-of-switching-to-new-azure-api) via Azure-portalen samt
- Allvarlighetsgrad för loggvarningsregler flyttas från: *Kritisk, Varning & Informations-* och *allvarlighetsgraden 0, 1 & 2*. Tillsammans med alternativet att skapa/ uppdatera varningsregler med allvarlighetsgrad 3 och 4 också.

Processen med att flytta varningsregler från [äldre Log Analytics Alert API](api-alerts.md) innebär inte att ändra din varningsdefinition, fråga eller konfiguration på något sätt. Dina varningsregler och övervakning påverkas inte och aviseringarna stoppas inte eller stoppas, under eller efter växeln. De enda förändringarna är:

- En ändring av API-inställningar och åtkomst till dina regler via ett nytt API.
- En uri för en modifierad aviseringsregel som innehåller de ID:n `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`som används i det äldre Log Analytics Alert [API:et](api-alerts.md) i stället för varningsregelnamnet i den här strukturen . Visningsnamnet på varningsregeln förblir oförändrat.

Alla kunder som frivilligt vill växla till den nya [schemalagdaQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och blockera användning från det [äldre Log Analytics Alert API;](api-alerts.md) kan göra det genom att utföra ett PUT-anrop på api:et nedan för att växla alla varningsregler som är associerade med den specifika Log Analytics-arbetsytan.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Med begäran kropp som innehåller nedan JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

API:et kan också nås från en PowerShell-kommandorad med [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg med öppen källkod som förenklar att anropa Azure Resource Manager API. Som visas nedan, i exempel PUT-anrop med ARMclient-verktyget för att växla alla varningsregler som är associerade med den specifika Log Analytics-arbetsytan.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Om byte av alla varningsregler på log analytics-arbetsytan för att använda nya [schemalagdaQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) lyckas, kommer följande svar att tillhandahållas.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Användare kan också kontrollera aktuell status för din Log Analytics-arbetsyta och se om den har eller inte har bytts ut för att endast använda [schemalagdaqueryrules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) För att kontrollera, användare kan utföra ett GET-samtal på nedanstående API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om du vill köra ovanstående med powershell-kommandoraden med [armclient-verktyget](https://github.com/projectkudu/ARMClient) läser du exemplet nedan.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om den angivna log analytics-arbetsytan har växlats till att endast använda [schemalagdaqueryrules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) då svaret JSON kommer att vara som anges nedan.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Annars, om den angivna logalytiska arbetsytan ännu inte har växlats till att endast använda [schemalagdaqueryrules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) då svaret JSON kommer att vara som anges nedan.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Monitor - Loggaviseringar](alerts-unified-log.md).
- Lär dig hur du skapar [loggaviseringar i Azure-aviseringar](alerts-log.md).
- Läs mer om [Azure Alerts-upplevelsen](../../azure-monitor/platform/alerts-overview.md).
