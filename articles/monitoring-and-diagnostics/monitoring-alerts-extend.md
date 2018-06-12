---
title: Utöka (kopiera) logganalys aviseringar i Azure varningar – översikt
description: Översikt över processen för att kopiera aviseringar från logganalys i OMS-portalen till Azure-aviseringar med detaljerad information om adressering vanliga kundernas problem.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: c01f986bfe3e6a3162988056a268423e07f429bf
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301158"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Utöka logganalys aviseringar till Azure-aviseringar
Tills nyligen med Azure logganalys sin egen avisering funktioner, vilket kan proaktivt meddela du villkor baserade på Log Analytics-data. Hanteras av Varningsregler i den [Microsoft Operations Management Suite-portalen](../operations-management-suite/operations-management-suite-overview.md). Den nya upplevelsen av aviseringar har nu integrerats aviseringar i olika tjänster i Microsoft Azure. Detta är tillgänglig för **aviseringar** under Azure-Monitor i Azure-portalen och stöder aviseringar från aktivitetsloggar, mått, och loggar från både logganalys och Azure Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Fördelar med att utöka dina aviseringar
Det finns flera fördelar med att skapa och hantera aviseringar i Azure-portalen som:

- Till skillnad från i Operations Management Suite-portalen där endast 250 aviseringar kan skapas och visas, har Azure aviseringar ingen sådan begränsning.
- Från Azure aviseringar kan du hantera, räkna upp och visa alla aviseringstyper. Tidigare kunde endast gör du det för logganalys-aviseringar.
- Du kan begränsa åtkomsten till användare kan bara övervakning och avisering med hjälp av den [Azure-Monitor rollen](monitoring-roles-permissions-security.md).
- Du kan använda i Azure aviseringar [åtgärdsgrupper](monitoring-action-groups.md). Detta gör att du har mer än en åtgärd för varje avisering. Du kan SMS, skicka ett röstsamtal, anropa en Azure Automation-runbook, anropa en webhook och konfigurera en IT Service Management (ITSM)-anslutning. 

## <a name="process-of-extending-your-alerts"></a>Utöka aviseringar
Processen att flytta aviseringar från Log Analytics till Azure aviseringar inbegriper inte ändra din varningsdefinitionen, fråga eller konfiguration på något sätt. Den enda förändringen som krävs är att i Azure måste du utföra alla åtgärder med hjälp av en grupp. Om åtgärdsgrupper redan är associerad med aviseringen inkluderas de när utökats till Azure.

> [!NOTE]
> Microsoft utöka aviseringar som skapats i Log Analytics till Azure aviseringar automatiskt startar för den 14 maj 2018 i en serie med återkommande tills slutförts. Om du har några problem med att skapa [åtgärdsgrupper](monitoring-action-groups.md), använda [dessa steg](monitoring-alerts-extend-tool.md#troubleshooting) att hämta åtgärdsgrupper skapas automatiskt. Du kan använda de här stegen tills 5 juli 2018. 
> 

När du schemalägger aviseringar i logganalys-arbetsytan utökas till Azure kan fortsätta de att fungera och inte i något sätt skada din konfiguration. Schemalagd aviseringarna kanske inte tillgänglig för redigering tillfälligt, men du kan fortsätta att skapa nya aviseringar i Azure under denna tid. Om du försöker redigera eller skapa aviseringar från Operations Management Suite-portalen har du möjlighet att fortsätta skapa dem från logganalys-arbetsytan. Du kan också välja att skapa dem från Azure-aviseringar i Azure-portalen.

 ![Skärmbild av alternativet för att skapa aviseringar från Log Analytics eller Azure-aviseringar](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Utöka aviseringar från Log Analytics till Azure inte avgifter till ditt konto. Med hjälp av Azure aviseringar för fråga baserad logganalys aviseringar inte debiteras när den används inom gränserna och villkor som anges i den [Azure-Monitor priser princip](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Hur du utökar aviseringarna frivilligt
Du kan använda en guide som är tillgänglig i Operations Management Suite-portalen för att utöka dina aviseringar i Azure aviseringar eller du kan göra så programmässigt med hjälp av en API. Mer information finns i [utöka aviseringar i Azure med hjälp av Operations Management Suite-portal och API](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Uppstår när du utökar dina aviseringar
När aviseringar har utökats till Azure aviseringar fortsätter de att vara tillgängliga i Operations Management Suite-portalen för hantering av inte annorlunda än tidigare.

![Skärmbild av Operations Management Suite-portalen med aviseringarna som visas](./media/monitor-alerts-extend/PostExtendList.png)

När du försöker redigera en befintlig avisering eller skapa en ny avisering i Operations Management Suite-portalen, omdirigeras du automatiskt till Azure-aviseringar.  

> [!NOTE]
> Se till att de behörigheter som tilldelats till personer som behöver lägga till eller redigera aviseringar tilldelas korrekt i Azure. Information om vilka behörigheter som du behöver ge finns [behörigheter för att använda Azure-Monitor och aviseringar](monitoring-roles-permissions-security.md).  
> 

Du kan fortsätta att skapa aviseringar från den [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) och [Log Analytics Resource mallen](../monitoring/monitoring-solutions-resources-searches-alerts.md). När du gör det måste du inkludera åtgärdsgrupper.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om verktyg för [initiera utöka aviseringar från logganalys i Azure](monitoring-alerts-extend-tool.md).
* Lär dig mer om den [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig hur du skapar [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
