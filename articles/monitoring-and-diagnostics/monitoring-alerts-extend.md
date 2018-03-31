---
title: Utöka aviseringar från OMS till Azure - översikt | Microsoft Docs
description: Översikt över processen för att utöka aviseringar från OMS i Azure aviseringar, information kring vanliga kunden frågor.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: abab73a047fb923fa2cd57a1c7194e440697bf64
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="extend-alerts-from-oms-into-azure"></a>Utöka aviseringar från OMS till Azure
Den nya upplevelsen av aviseringar har nu integrerats aviseringar upplevelsen över olika tjänster och delar i Microsoft Azure. Den nya upplevelsen som är tillgängliga som **aviseringar** under Övervakare för Azure-portalen har hämtat tillsammans i en gemensam plats - aktivitet loggen aviseringar, mått aviseringar och logga varningar på logganalys samt Application Insights. 

Men för vissa användare, logganalys och allied funktioner som aviseringar, har via [Microsoft åtgärden Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Och därmed så att de enkelt kan hantera sina andra Azure-resurser tillsammans med deras användning av logganalys - systematiskt Microsoft har säkerställa funktionerna i OMS finns också tillgängliga i Azure-portalen. På så sätt att hänsyn Azure aviseringar redan tillåter användare att hantera frågebaserade aviseringar för Log Analytics mer information finns i [Logga varningar på Azure-aviseringar](monitor-alerts-unified-log.md). Aviseringar som skapats i OMS finns redan under lämplig log analytics-arbetsyta i aviseringar under Azure-Monitor. Men alla redigering eller ändra på dessa aviseringar som skapats i OMS, kräver att användaren lämnar Azure och använda OMS; Gå sedan tillbaka till Azure om de behövs för att hantera alla andra tjänster. För att minska den här svårigheter Microsoft nu så att användarna kan utöka sina aviseringar från OMS till Azure.

## <a name="benefits-of-extending-your-alerts"></a>Fördelar med att utöka dina aviseringar
Förutom fördelar som uppstått i inte behöva gå utanför Azure-portalen, finns det andra fördelar med viktigaste i Utöka aviseringar från OMS i Azure

- Till skillnad från i OMS där endast 250 aviseringar kan skapas och visas; i Azure aviseringar finns den här begränsningen inte
- Från Azure aviseringar kan alla aviseringstyper hanteras, räknas upp och visat; inte bara logganalys aviseringar som är fallet med OMS
- Använda Azure aviseringar [åtgärdsgrupper](monitoring-action-groups.md), vilket kan du ha mer än en åtgärd för varje avisering inklusive SMS, röst anropa, Automation-Runbook, Webhook, ITSM koppling och mer. Medan OMS aviseringar är begränsade i båda numret samt som typ av åtgärder som möjligt

## <a name="process-of-extending-your-alerts"></a>Utöka aviseringar
Processen för att utöka aviseringar från OMS i Azure, har **inte** innebär bland annat ändring varningsdefinitionen, frågan eller konfiguration på något sätt. Den enda förändringen som krävs är att webhook-anrop med automation-runbook eller ansluta till ITSM verktyget görs via grupp i Azure, alla åtgärder, till exempel e-postmeddelande. Därför om lämpliga åtgärdsgrupp är associerade med aviseringen - ska de bli utökats till Azure.

Eftersom processen med att utöka är icke-förstörande och inte interruptive, Microsoft förlänga aviseringar skapas i Operations Management Suite (OMS) till Azure-aviseringar automatiskt – från och med **23 April 2018**. Från den aktuella dagen Microsoft börjar att schemalägga utöka aviseringar i Azure och gradvis se alla varningar som finns i OMS, hanterbar från Azure-portalen. 

När aviseringar i en OMS-arbetsyta hämta schemat för att utöka till Azure, de fortsätter att fungera och kommer **inte** på något sätt påverka övervakningen. Schemalagd aviseringarna kanske inte är tillgänglig för ändring/redigering tillfälligt; men nya Azure aviseringar kan fortsätta att skapas i den här kort tid. I den här korta perioden om alla redigera eller skapa avisering görs från OMS måste har användare alternativet för att fortsätta i Azure Log Analytics eller Azure-aviseringar.

 ![Under schemalagda användaråtgärd på aviseringar dirigeras till Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Utöka aviseringar från Operations Management Suite (OMS) till Azure debiteras inte och användning av Azure aviseringar för frågebaserade logganalys aviseringar inte debiteras, när de används de begränsningar och villkor som anges i [Azure-Monitor priser princip](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Användare kan dra nytta av fördelarna med att utöka aviseringar innan detta datum. genom att inaktivera frivilligt så att deras aviseringar kan hanteras i Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Hur du utökar frivilligt aviseringar
Om du vill att OMS användarna ett enkelt pass i Azure-aviseringar, har Microsoft utvecklat verktyg för att aktivera utöka aviseringarna. Microsoft Operations Management Suite (OMS) kunder kan utöka sina aviseringar till Azure antingen från en guide i OMS-portalen (eller) av en programmässiga metod med ett nytt API. Mer information finns i [utöka aviseringar i Azure med hjälp av OMS-portalen och API](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Användning när du utökar dina aviseringar
Som nämnts kan utökas aviseringar som skapats i Microsoft åtgärden Management Suite till Azure aviseringar; efter vilket du kan hantera dem från Azure. Aviseringar fortsätter att visas i OMS-portalen - varningsinställning avsnitt. Som på bilden nedan:

 ![Visar en lista över aviseringar efter att förlängas till Azure OMS-portalen](./media/monitor-alerts-extend/PostExtendList.png)

För någon åtgärd aviseringar som redigera eller skapa utförs i OMS-portalen för dirigeras användare transparent till Azure-aviseringar. Varna skapa fortsätter från det befintliga [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) som tidigare, med bara mindre ändring är att när aviseringar har utökats till Azure - åtgärdsgrupper måste associeras i schemat.

## <a name="next-steps"></a>Nästa steg

* Lär dig verktyg för [initiera utöka aviseringar från OMS i Azure](monitoring-alerts-extend-tool.md)
* Mer information om den nya [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
