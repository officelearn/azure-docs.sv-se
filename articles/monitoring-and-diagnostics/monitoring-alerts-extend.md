---
title: Utöka (kopiera) Log Analytics-aviseringar i Azure Alerts - översikt
description: Översikt över processen för att kopiera aviseringar från Log Analytics i OMS-portalen till Azure-aviseringar med detaljerad information om adressering vanliga kundernas synpunkter.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 60ecc2bc1d700c223d4a52b05e3b9362241f3f82
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414627"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Utöka Log Analytics-aviseringar till Azure-aviseringar
Tills nyligen med Azure Log Analytics sin egen avisering funktioner, vilket kan informera dig om villkor baserat på Log Analytics-data. Du har hanterat Varningsregler i Microsoft Operations Management Suite-portalen. Det nya aviseringsgränssnittet har nu integrerats aviseringar för olika tjänster i Microsoft Azure. Detta är tillgänglig **aviseringar** under Azure Monitor i Azure-portalen och har stöd för aviseringar från aktiviteten skrivloggar, statistik och loggar från både Log Analytics och Azure Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Fördelar med att utöka dina aviseringar
Det finns flera fördelar med att skapa och hantera aviseringar i Azure portal, till exempel:

- Till skillnad från i Operations Management Suite-portalen där endast 250 aviseringar kan skapas och visas, har Azure-aviseringar ingen sådan begränsning.
- Från Azure-aviseringar kan du hantera, räkna upp och visa alla dina aviseringstyper. Tidigare var kan du bara göra det för Log Analytics-aviseringar.
- Du kan begränsa åtkomsten till användarna bara övervakning och avisering med hjälp av den [Azure Monitor rollen](monitoring-roles-permissions-security.md).
- Du kan använda i Azure Alerts [åtgärdsgrupper](monitoring-action-groups.md). På så sätt kan du ha mer än en åtgärd för varje avisering. Du kan SMS, skicka en röstsamtal, anropar en Azure Automation-runbook, anropa en webhook och konfigurera en anslutning för IT Service Management (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Utöka dina aviseringar
Processen att flytta aviseringar från Log Analytics i Azure Alerts inbegriper inte ändra aviseringens definition, fråga eller konfiguration på något sätt. Den enda ändringen som krävs är att i Azure måste du utföra alla åtgärder med hjälp av en åtgärdsgrupp. Om åtgärdsgrupper redan är kopplad till aviseringen, inkluderas de när utökad till Azure.

> [!NOTE]
> Microsoft aviseringar automatiskt skapade i offentligt moln instanser av Log Analytics till Azure-aviseringar startar för den 14 maj 2018 i en serie med återkommande tills slutförts. Om du har några problem med att skapa [åtgärdsgrupper](monitoring-action-groups.md), använda [dessa åtgärdssteg](monitoring-alerts-extend-tool.md#troubleshooting) att hämta åtgärdsgrupper som skapas automatiskt. Du kan använda de här stegen till 5 juli 2018. *Inte tillämpligt för Azure Government och nationellt molnanvändare av Log Analytics*. 
> 

När du schemalägger aviseringar i Log Analytics-arbetsytan utökas till Azure kan fortsätta de att fungera och inte i något sätt kompromettera din konfiguration. När schemalagts dina aviseringar kanske inte tillgänglig för ändring av tillfälligt, men du kan fortsätta att skapa nya Azure-aviseringar under den här tiden. Om du försöker redigera eller skapa aviseringar från Operations Management Suite-portalen, har du möjlighet att fortsätta att skapa dem från Log Analytics-arbetsytan. Du kan också välja att skapa dem från Azure-aviseringar i Azure-portalen.

 ![Skärmbild av alternativet för att skapa aviseringar från Log Analytics eller Azure-aviseringar](media/monitoring-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Utöka aviseringar från Log Analytics till Azure leder inte till att kontot debiteras. Med hjälp av Azure-aviseringar för fråga efter Log Analytics-aviseringar debiteras inte när den används inom gränserna och villkor som anges i den [Azure Monitor priser princip](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Utöka dina aviseringar frivilligt
Du kan använda en guide som är tillgängliga i Operations Management Suite-portalen för att utöka aviseringarna till Azure-aviseringar, eller du kan göra programmering med hjälp av ett API. Mer information finns i [utöka aviseringar i Azure med hjälp av Operations Management Suite-portalen och API](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Upplevelse när du utökar dina aviseringar
När dina aviseringar utsträcks till Azure-aviseringar, fortsätta de att vara tillgänglig i Operations Management Suite-portalen för hantering av inte annorlunda än tidigare.

![Skärmbild av Operations Management Suite-portalen med aviseringarna som visas](media/monitoring-alerts-extend/PostExtendList.png)

Du omdirigeras automatiskt till Azure-aviseringar när du försöker redigera en befintlig avisering eller skapa en ny avisering i Operations Management Suite-portalen.  

> [!NOTE]
> Se till att de behörigheter som tilldelats till personer som behöver lägga till eller redigera aviseringar tilldelas korrekt i Azure. Information om vilka behörigheter som du måste bevilja finns i [behörigheter för att använda Azure Monitor och aviseringar](monitoring-roles-permissions-security.md).  
> 

Du kan fortsätta att skapa aviseringar från den [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) och [Log Analytics-resursmall](../monitoring/monitoring-solutions-resources-searches-alerts.md). När du gör detta måste du inkludera åtgärdsgrupper.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om verktyg för att [initiera utöka aviseringar från Log Analytics i Azure](monitoring-alerts-extend-tool.md).
* Läs mer om den [Azure Alerts uppleva](monitoring-overview-unified-alerts.md).
* Lär dig hur du skapar [loggaviseringar i Azure Alerts](monitor-alerts-unified-log.md).
