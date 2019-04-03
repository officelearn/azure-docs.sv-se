---
title: Uppdateringar av Azure-terminologi | Microsoft Docs
description: Beskriver ändringar gjorts i Azures övervakningstjänster terminologi.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847996"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor-namngivning och terminologi ändringar
Viktiga ändringar har gjorts till Azure Monitor nyligen med olika tjänster som konsolideras för att underlätta övervakning för Azure-kunder. Den här artikeln beskriver de senaste namn och terminologi ändringar i Azure Monitor-dokumentationen.

## <a name="february-2019---log-analytics-terminology"></a>Februari 2019 - Log Analytics-terminologi
Efter konsolideringen av olika tjänster under Azure Monitor tar vi nästa steg genom att ändra terminologi i dokumentationen för att bättre beskriva Azure Monitor-tjänsten och dess olika komponenter. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor log data är fortfarande lagras i Log Analytics-arbetsytan och fortfarande samlas in och analyseras av samma Log Analytics-tjänsten, men ändrar vi termen _Log Analytics_ på många platser till _Azure Monitor-loggar_ . Den här termen bättre motsvarar dess roll i Azure Monitor och ger bättre konsekvens [mått i Azure Monitor](platform/data-platform-metrics.md).

Termen _logganalys_ nu gäller främst för sidan i Azure portal som används för att skriva och köra frågor och analysera loggdata. Det är den funktionella motsvarigheten till [måttutforskaren](platform/metrics-charts.md), vilket är sidan i Azure portal som används för att analysera måttdata.

### <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor
[Arbetsytor](platform/manage-access.md) som håller loggdata i Azure Monitor kallas fortfarande Log Analytics-arbetsytor. Den **Log Analytics** menyn i Azure-portalen har bytt namn till **Log Analytics-arbetsytor** och är där du [skapa nya arbetsytor](learn/quick-create-workspace.md) och konfigurera datakällor. Analysera dina loggar och andra övervakningsdata i **Azure Monitor** och konfigurera din arbetsyta i **Log Analytics-arbetsytor**.

### <a name="management-solutions"></a>Hanteringslösningar
[Lösningar för hantering av](insights/solutions.md) har bytt namn till _övervakningslösningar_, vilket bättre beskriver deras funktioner.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Augusti 2018 - konsolidering av övervakningstjänster i Azure Monitor
Log Analytics och Application Insights har integrerats i Azure Monitor för att tillhandahålla en enda integrerad upplevelse för att övervaka Azure-resurser och hybridmiljöer. Inga funktioner har tagits bort från de här tjänsterna och användarna kan utföra samma scenarier som de alltid har slutförts utan förlust eller en kompromettering av alla funktioner.

Dokumentation för var och en av dessa tjänster har integrerats i en enda uppsättning innehåll för Azure Monitor. Detta hjälper dig att läsaren hitta allt innehåll för ett visst scenario för övervakning på en enda plats istället för att referera till flera uppsättningar av innehåll du behöver. Eftersom tjänsten konsoliderade utvecklas, kommer innehållet blir mer integreras.

Andra funktioner som har som anses del av Log Analytics som agenter och vyer har också tagits flyttas som funktioner i Azure Monitor. Deras funktioner har inte förändrats än möjliga förbättringar av deras upplevelse i Azure-portalen.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>April 2018 - tillbakadragning av Operations Management Suite varumärke
Operations Management Suite (OMS) har en sammanslagning av följande Azure management-tjänster för licensiering:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Ny prisnivå har introducerats för dessa tjänster](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), och OMS paketera är inte längre tillgänglig för nya kunder. Ingen av de tjänster som ingick i OMS har ändrats, förutom konsolideringen i Azure Monitor som beskrivs ovan. 




## <a name="next-steps"></a>Nästa steg

- Läs en [översikt över Azure Monitor](overview.md) som beskriver dess olika komponenter och funktioner.
- Lär dig mer om den [övergång av OMS-portalen](../log-analytics/log-analytics-oms-portal-transition.md).