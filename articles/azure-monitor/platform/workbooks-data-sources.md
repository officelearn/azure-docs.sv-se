---
title: Datakällor för Azure Monitor-arbetsböcker | Microsoft-dokument
description: Förenkla komplex rapportering med fördefinierade och anpassade parameteriserade Azure Monitor-arbetsböcker byggda från flera datakällor
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248585"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Datakällor för Azure Monitor-arbetsböcker

Arbetsböcker är kompatibla med ett stort antal datakällor. Den här artikeln kommer att gå igenom datakällor som för närvarande är tillgängliga för Azure Monitor-arbetsböcker.

## <a name="logs"></a>Loggar

Arbetsböcker tillåter att du frågar logger från följande källor:

* Azure Monitor Loggar (Program Insights Resources och Log Analytics Workspaces.)
* Resurscentrerade data (aktivitetsloggar)

Arbetsboksförfattare kan använda KQL-frågor som omvandlar underliggande resursdata för att välja en resultatuppsättning som kan visualiseras som text, diagram eller rutnät.

![Skärmbild av rapportgränssnittet för arbetsböckerloggar](./media/workbooks-overview/logs.png)

Arbetsboksförfattare kan enkelt fråga över flera resurser och skapa en verkligt enhetlig omfattande rapporteringsupplevelse.

## <a name="metrics"></a>Mått

Azure-resurser släpper ut [mått](data-platform-metrics.md) som kan nås via arbetsböcker. Mått kan nås i arbetsböcker via en specialiserad kontroll som gör att du kan ange målresurser, önskade mått och deras aggregering. Dessa data kan sedan ritas i diagram eller rutnät.

![Skärmbild av arbetsboksmått diagram för cpu-användning](./media/workbooks-overview/metrics-graph.png)

![Skärmbild av gränssnitt för arbetsboksmått](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Arbetsböcker stöder frågor om resurser och deras metadata med hjälp av Azure Resource Graph (ARG). Den här funktionen används främst för att skapa anpassade frågeomfattningar för rapporter. Resursomfattningen uttrycks via en KQL-delmängd som ARG stöder – vilket ofta är tillräckligt för vanliga användningsfall.

Om du vill att en frågekontroll ska använda den här datakällan använder du listrutan Frågetyp för att välja Azure Resource Graph och välja prenumerationer som ska riktas. Använd frågekontrollen för att lägga till ARG KQL-delmängden som väljer en intressant resursdelmängd.


![Skärmbild av KQL-frågan för Azure Resource Graph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Varningar (förhandsgranskning)

Arbetsböcker gör det möjligt för användare att visualisera aktiva aviseringar som är relaterade till sina resurser. Med den här funktionen kan du skapa rapporter som samlar meddelandedata (aviseringar) och diagnostikinformation (mått, loggar) i en rapport. Den här informationen kan också sammanfogas för att skapa omfattande rapporter som kombinerar insikter över dessa datakällor.

Om du vill att en frågekontroll ska använda den här datakällan använder du listrutan Frågetyp för att välja Aviseringar och väljer prenumerationer, resursgrupper eller resurser som ska riktas. Använd listrutan för varningsfilter för att välja en intressant delmängd av aviseringar för dina analytiska behov.

![Skärmbild av fråga om aviseringar](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Arbetsbelastningshälsa (förhandsgranskning)

Azure Monitor har funktioner som proaktivt övervakar tillgängligheten och prestanda för Windows eller Linux gästoperativsystem. Azure Monitor modellerar viktiga komponenter och deras relationer, kriterier för hur du mäter hälsotillståndet för dessa komponenter och vilka komponenter som varnar dig när ett feltillstånd upptäcks. Arbetsböcker gör det möjligt för användare att använda den här informationen för att skapa omfattande interaktiva rapporter.

Om du vill att en frågekontroll ska använda den här datakällan använder du listrutan **Frågetyp** för att välja Arbetsbelastningshälsa och väljer prenumerations-, resursgrupp- eller VM-resurser att rikta in sig på. Använd listrutan hälsofilter för att välja en intressant delmängd av hälsoincidenter för dina analytiska behov.

![Skärmbild av fråga om aviseringar](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Hälsotillstånd för Azure-resurser 

Arbetsböcker stöder att azure-resurshälsan och kombinerar den med andra datakällor för att skapa omfattande, interaktiva hälsorapporter

Om du vill att en frågekontroll ska använda den här datakällan använder du listrutan **Frågetyp** för att välja Azure-hälsotillstånd och väljer de resurser som ska riktas. Använd listrutan hälsofilter för att välja en intressant delmängd av resursproblem för dina analytiska behov.

![Skärmbild av fråga om aviseringar](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (förhandsgranskning)

Arbetsböcker har nu stöd för frågor från [Azure Data Explorer-kluster](https://docs.microsoft.com/azure/data-explorer/) med det kraftfulla Kusto-frågespråket. [Kusto](https://docs.microsoft.com/azure/kusto/query/index)   

![Skärmbild av Kusto-frågefönstret](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
