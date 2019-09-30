---
title: 'Växla från äldre Log Analytics aviserings-API till nya API: er för Azure-aviseringar'
description: 'Översikt över äldre savedSearch-baserade Log Analytics aviserings-API och process för att växla varnings regler till nya ScheduledQueryRules-API: er med information som behandlar vanliga kund problem.'
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 7b3a09c9227110d6dba205987903a2c97dccf1b8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677790"
---
# <a name="switch-api-preference-for-log-alerts"></a>Byta API-inställning för loggaviseringar

> [!NOTE]
> Innehåll som anges gäller endast för användare Azures offentliga moln och **inte** för Azure Government-eller Azure Kina-moln.  

Till nyligen hanterade du aviseringsregler i Microsoft Operations Management Suite-portalen. Den nya aviserings upplevelsen har integrerats med olika tjänster i Microsoft Azure inklusive Log Analytics och vi har bett att [Utöka dina aviserings regler från OMS-portalen till Azure](alerts-extend.md). Men för att säkerställa minimalt avbrott för kunderna ändrade processen inte programmerings gränssnittet för dess användnings [Log Analytics aviserings-API](api-alerts.md) baserat på SavedSearch.

Men nu kan du meddela Log Analytics aviserar användare om ett äkta Azure-programalternativ, [Azure Monitor-SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), som också reflekteras i din [Azure-fakturering – för logg aviseringar](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Om du vill veta mer om hur du hanterar logg aviseringar med hjälp av API: et, se [Hantera logg aviseringar med Azure-resurs mal len](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [Hantera logg aviseringar med PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Fördelar med att växla till New Azure-API

Det finns flera fördelar med att skapa och hantera aviseringar med hjälp av [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) över [äldre Log Analytics aviserings-API](api-alerts.md); Vi har listat några av de viktigaste nedan:

- Möjlighet att [söka mellan arbets ytans loggs ökning](../log-query/cross-workspace-query.md) i aviserings regler och utöka externa resurser som Log Analytics arbets ytor eller till och med Application Insights appar
- När flera fält används för att gruppera i frågor kan du använda [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -användare för att ange vilket fält som ska aggregeras i Azure Portal
- Logg aviseringar som skapats med [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan ha en definierad period på upp till 48 timmar och hämta data under en längre period än tidigare
- Skapa aviserings regler i en bild som en enskild resurs utan att behöva skapa tre resurs nivåer som med [äldre Log Analytics aviserings-API](api-alerts.md)
- Ett enda programmerings gränssnitt för alla varianter av frågor baserade logg aviseringar i Azure – New [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan användas för att hantera regler för Log Analytics samt Application Insights
- Hantera dina logg aviseringar med [PowerShell-cmdletar](alerts-log.md#managing-log-alerts-using-powershell)
- Alla nya logg aviserings funktioner och framtida utveckling kommer bara att vara tillgängliga via den nya [scheduledQueryRules-API: n](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Process för att växla från äldre logg aviserings-API

Användarna är kostnads fria att använda antingen [äldre Log Analytics aviserings-API](api-alerts.md) eller det nya [scheduledQueryRules-API: et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Aviserings regler som skapats av något av API: et kan *endast hanteras av samma API* – och från Azure Portal. Som standard fortsätter Azure Monitor att använda [äldre Log Analytics varnings-API](api-alerts.md) för att skapa en ny varnings regel från Azure Portal för befintliga arbets ytor i Log Analytics. Som [aviserad ny logg arbets yta som skapats den 1 juni 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) , kommer automatiskt att använda New [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) som standard, inklusive i Azure Portal.

Påverkan av växeln av preferens till scheduledQueryRules-API: n kompileras nedan:

- Alla interaktioner som är gjorda för att hantera logg aviseringar via programmatiska gränssnitt måste nu utföras med [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i stället. Mer information finns i, [exempel som används via Azure-resurs mal len](alerts-log.md#managing-log-alerts-using-azure-resource-template) och [exempel användning via PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Alla nya logg aviserings regler som skapats i Azure Portal skapas endast med [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och tillåter användare att använda [ytterligare funktioner i det nya API: et](#benefits-of-switching-to-new-azure-api) via Azure Portal också
- Allvarlighets graden för logg aviserings regler flyttas från: *Kritisk, varning & information*till *allvarlighets graderna 0, 1 & 2*. Tillsammans med alternativet för att skapa/uppdatera aviserings regler med allvarlighets grad 3 och 4.

Processen för att flytta aviserings regler från [äldre Log Analytics varnings-API: t](api-alerts.md) inbegriper inte ändring av aviserings definition, fråga eller konfiguration på något sätt. Aviserings reglerna och övervakningen påverkas inte och aviseringarna kommer inte att stoppas eller stoppas, under eller efter växeln. Den enda ändringen är en ändring av API-inställningen och åtkomst till dina regler via ett nytt API.

> [!NOTE]
> När en användare väljer att växla inställningar till det nya [scheduledQueryRules-API: et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)kan du inte välja tillbaka eller återgå till att använda äldre [aviserings-API: er för äldre Log Analytics](api-alerts.md).

En kund som vill växla frivilligt till den nya [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och blockera användningen från den [äldre Log Analytics varnings-API: et](api-alerts.md). kan göra detta genom att utföra ett parkera-anrop på nedanstående API för att växla alla aviserings regler som är kopplade till den aktuella Log Analytics-arbetsytan.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Med en begär ande text som innehåller nedanstående JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

API: et kan också nås från en PowerShell-kommandorad med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient), ett kommando rads verktyg med öppen källkod som gör det enklare att anropa Azure Resource Manager API. Som illustreras nedan, i exempel skicka samtal med ARMclient-verktyget för att växla alla aviserings regler som är kopplade till den aktuella Log Analytics-arbetsytan.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Om switchen av alla aviserings regler i Log Analytics arbets ytan för att använda den nya [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) har slutförts, kommer följande svar att tillhandahållas.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Användare kan också kontrol lera aktuell status för din Log Analytics arbets yta och se om den har eller inte har växlats till att använda [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . För att kontrol lera kan användarna utföra ett GET-anrop i nedanstående API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om du vill köra ovanstående kommando rad med PowerShell-kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient) -verktyget, se exemplet nedan.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om den angivna Log Analytics arbets ytan har växlats till att endast använda [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . sedan visas svars-JSON enligt listan nedan.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Annars, om den angivna logg analys arbets ytan ännu inte har växlats till att använda [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . sedan visas svars-JSON enligt listan nedan.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Monitor-logg aviseringar](alerts-unified-log.md).
- Lär dig hur du skapar [logg aviseringar i Azure-aviseringar](alerts-log.md).
- Lär dig mer om [Azures aviserings upplevelse](../../azure-monitor/platform/alerts-overview.md).
