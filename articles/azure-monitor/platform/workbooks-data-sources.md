---
title: Skapa interaktiva rapporter med Azure Monitor arbets böcker från flera dispate Azure-datakällor | Microsoft-dokument
description: Förenkla komplex rapportering med färdiga och anpassade parameterstyrda Azure Monitor arbets böcker som skapats från flera data källor
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dd5068da31c3aa863fc56022834a28b60ee15004
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166205"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor arbets böcker data källor

Arbets böcker är kompatibla med ett stort antal data källor. Den här artikeln vägleder dig genom data källor som för närvarande är tillgängliga för Azure Monitor arbets böcker.

## <a name="logs"></a>Loggar

Med arbets böcker kan du skicka frågor till loggar från följande källor:

* Azure Monitor loggar (Application Insights resurser och Log Analytics arbets ytor.)
* Resursbaserade data (aktivitets loggar)

Arbets boks författare kan använda KQL frågor som transformerar underliggande resurs data för att välja en resultat uppsättning som kan visualiseras som text, diagram eller rutnät.

![Skärm bild av rapport gränssnitt för arbets boks loggar](./media/workbooks-overview/logs.png)

Arbets boks författare kan enkelt fråga över flera resurser som skapar en verkligt enhetlig rapport upplevelse.

## <a name="metrics"></a>Mått

Azure-resurser genererar [mått](data-platform-metrics.md) som kan nås via arbets böcker. Mått kan nås i arbets böcker via en specialiserad kontroll som gör att du kan ange mål resurserna, önskade mått och deras agg regering. Dessa data kan sedan ritas i diagram eller rutnät.

![Skärm bild av mått diagram för arbets böcker med processor användning](./media/workbooks-overview/metrics-graph.png)

![Skärm bild av gränssnittet för arbets boks mått](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Arbets böcker stöder frågor om resurser och deras metadata med hjälp av Azure Resource Graph (ARG). Den här funktionen används främst för att bygga anpassade fråge omfattningar för-rapporter. Resurs omfånget uttrycks via en KQL-delmängd som ARG stöder – vilket ofta är tillräckligt för vanliga användnings fall.

Om du vill att en fråga ska använda den här data källan använder du List rutan typ av fråga för att välja Azure Resource Graph och väljer de prenumerationer som du vill använda. Använd kontrollen fråga för att lägga till den ARG KQL-delmängd som väljer en intressant resurs del uppsättning.


![Skärm bild av Azure Resource Graph KQL-fråga](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Aviseringar (för hands version)

Med arbets böcker kan användare visualisera aktiva aviseringar relaterade till deras resurser. Med den här funktionen kan du skapa rapporter som sammanställer aviserings data (avisering) och diagnostisk information (mått, loggar) i en rapport. Den här informationen kan också kopplas ihop för att skapa omfattande rapporter som kombinerar insikter över dessa data källor.

Om du vill att en fråga ska använda den här data källan använder du List rutan typ av fråga för att välja aviseringar och väljer de prenumerationer, resurs grupper eller resurser som ska användas som mål. Använd List rutorna för varnings filter för att välja en intressant del av aviseringarna för dina analys behov.

![Skärm bild av fråga om aviseringar](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Arbets belastnings hälsa (för hands version)

Azure Monitor har funktioner som proaktivt övervakar tillgänglighet och prestanda för Windows-eller Linux-gäst operativ system. Azure Monitor modeller nyckel komponenter och deras relationer, kriterier för hur man mäter hälsan för dessa komponenter och vilka komponenter som varnar dig när ett dåligt hälso tillstånd upptäcks. Med arbets böcker kan användarna använda den här informationen för att skapa interaktiva interaktiva rapporter.

Om du vill att en fråga ska använda den här data källan använder du List rutan **typ av fråga** för att välja arbets belastnings hälsa och väljer prenumeration, resurs grupp eller VM-resurser som mål. Använd List rutorna för hälso filter för att välja en intressant del av hälso incidenter för dina analys behov.

![Skärm bild av fråga om aviseringar](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health 

Arbets böcker har stöd för att få Azure Resource Health och kombinera dem med andra data källor för att skapa avancerade, interaktiva hälso rapporter

Om du vill att en fråga ska använda den här data källan använder du List rutan **typ av fråga** för att välja Azure Health och väljer de resurser som ska användas som mål. Använd List rutorna för hälso filter för att välja en intressant del av resurs problem för dina analys behov.

![Skärm bild av fråga om aviseringar](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Datautforskaren (för hands version)

Arbets böcker har nu stöd för frågor från [Azure datautforskaren](https://docs.microsoft.com/azure/data-explorer/) -kluster med det kraftfulla [Kusto](https://docs.microsoft.com/azure/kusto/query/index) -frågespråket.   

![Skärm bild av Kusto Query-fönster](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.
