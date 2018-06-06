---
title: Utöka (kopiera) logganalys aviseringar i Azure varningar – översikt | Microsoft Docs
description: Översikt över processen för att kopiera aviseringar från logganalys i OMS-portalen till Azure-aviseringar med detaljerad information om adressering vanliga kundernas problem.
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
ms.date: 05/24/2018
ms.author: vinagara
ms.openlocfilehash: 296bdc6939ef8ab67656e5805cd603b50bce211e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763161"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Utöka logganalys aviseringar till Azure-aviseringar
Tills nyligen med Azure logganalys sin egen avisering funktioner, vilket kan proaktivt meddela du villkor baserade på Log Analytics-data.  Hantering av Varningsregler utfördes i [Microsoft åtgärden Management Suite (OMS) portal](../operations-management-suite/operations-management-suite-overview.md). Den nya upplevelsen av aviseringar har nu integrerats aviseringar upplevelsen olika tjänster i Microsoft Azure. Den nya upplevelsen finns som **aviseringar** under Azure-Monitor i Azure-portalen och stöder aviseringar från aktivitetsloggar, mått, och loggar från både logganalys och Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Fördelar med att utöka dina aviseringar
Det finns flera fördelar med att skapa och hantera aviseringar i Azure-port som:

- Till skillnad från i OMS-portalen där endast 250 aviseringar kan skapas och visas; i Azure aviseringar finns den här begränsningen inte
- Från Azure-varningar kan alla aviseringstyper hanteras, räknas upp och visat; inte bara logganalys aviseringar som tidigare var fallet
- Kontrollera åtkomsten till användarna bara övervakning och avisering, med hjälp av [Azure-Monitor roll](monitoring-roles-permissions-security.md)
- Använda Azure aviseringar [åtgärdsgrupper](monitoring-action-groups.md), vilket gör att du har mer än en åtgärd för varje avisering inklusive SMS, skicka ett röstsamtal, anropa en Automation-Runbook, anropa en Webhook, konfigurera en ITSM koppling och mycket mer. 

## <a name="process-of-extending-your-alerts"></a>Utöka aviseringar
Processen att flytta aviseringar från Log Analytics till Azure aviseringar har **inte** innebär bland annat ändring varningsdefinitionen, frågan eller konfiguration på något sätt. Den enda förändringen som krävs är att i Azure, alla åtgärder, till exempel e-postmeddelande, ett webhook-anrop, kör en Automation-runbook eller ansluta till verktyget du ITSM utförs med hjälp av en grupp. Om åtgärdsgrupper är redan associerad med aviseringen - de ska tas med vid utökats till Azure.

> [!NOTE]
> Utöka Microsoft automatiskt aviseringar som skapats i Log Analytics till Azure-aviseringar startar på **14 maj 2018** i en serie med återkommande tills slutförts. Om det finns problem med att skapa [åtgärdsgrupper](monitoring-action-groups.md), kan användaren använda [steg visas](monitoring-alerts-extend-tool.md#troubleshooting) till **5 juli 2018** att hämta åtgärdsgrupper skapas automatiskt. 
> 

När aviseringar i logganalys-arbetsytan är schemalagda för att utöka till Azure, de fortsätter att fungera och kommer **inte** på något sätt skada din konfiguration. Schemalagd aviseringarna kanske inte är tillgänglig för ändring/redigering tillfälligt; men nya Azure aviseringar kan fortsätta att skapas under denna tid. Om du försöker redigera eller skapa aviseringar från OMS-portalen ska du har möjlighet att fortsätta skapa dem från logganalys-arbetsytan eller Azure-aviseringar i Azure-portalen.

 ![Under schemalagda användaråtgärd på aviseringar dirigeras till Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Utöka aviseringar från Log Analytics till Azure inte betalar avgifter till ditt konto och din användning av Azure-aviseringar för frågebaserade logganalys aviseringar inte debiteras när den används inom gränserna och villkor som anges i [Azure-Monitor priser princip](https://azure.microsoft.com/pricing/details/monitor/)  

Du kan dra nytta av fördelarna med att utöka aviseringar före det här datumet genom att frivilligt välja att flytta dem till Azure-aviseringar.

### <a name="how-to-voluntarily-extend-your-alerts"></a>Hur du utökar frivilligt aviseringar
Vi har tagit två metoder för att slutföra den här aktiviteten i arbetsytan för att utöka dina aviseringar i Azure-aviseringar.  Du kan göra detta antingen från en guide som är tillgängliga i OMS-portalen eller genom programmering med ett nytt API.  Mer information finns i [utöka aviseringar i Azure med hjälp av OMS-portalen och API](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Uppstår när du utökar dina aviseringar
När aviseringar har utökats till Azure aviseringar fortsätter de att vara tillgänglig i OMS-portalen för hantering av inte annorlunda än tidigare.<br><br> ![Visar en lista över aviseringar efter att förlängas till Azure OMS-portalen](./media/monitor-alerts-extend/PostExtendList.png)

När du försöker redigera en befintlig avisering eller skapa en ny avisering i OMS-portalen, omdirigeras du automatiskt till Azure-aviseringar.  

> [!NOTE]
> Det är nödvändigt för att säkerställa behörigheter till personer som behöver lägga till eller redigera aviseringar tilldelas korrekt i Azure.  Granska, [behörigheter för att använda Azure-Monitor och aviseringar](monitoring-roles-permissions-security.md) för att förstå vilka behörigheter som du behöver ge.  
> 

Varna skapa fortsätter att fungera från den [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) och [Log Analytics Resource mallen](../monitoring/monitoring-solutions-resources-searches-alerts.md), med endast en mindre ändra som måste tillämpas - åtgärdsgrupper måste tas med.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om verktyg för [initiera utöka aviseringar från logganalys i Azure](monitoring-alerts-extend-tool.md)
* Mer information om den nya [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig hur du skapar [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
