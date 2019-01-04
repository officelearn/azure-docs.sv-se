---
title: Översikt över Azure Behållarövervakning | Microsoft Docs
description: Den här artikeln innehåller en översikt över de olika metoderna som är tillgängliga i Azure för att övervaka behållare i Azure för att snabbt förstå en kluster-hälsa och tillgänglighet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 5fad6b29f250c94166e7023b65ea0ca4a4c6bd8b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974649"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Översikt över övervakning av behållare i Azure
Med Azure kan du effektivt övervaka och hantera dina arbetsbelastningar som distribuerats på Azure som kör Kubernetes eller Docker-behållare. Det är viktigt att förstå hur behållare med flera mikrotjänstprogram utför för att leverera en tillförlitlig tjänst i skala och övervakning planen. Den här artikeln innehåller en kort översikt över hanterings- och övervakningsfunktionerna i Azure för att förstå dem och som är lämplig baserat på dina krav.

Med hjälp av [Azure Monitor för behållare](container-insights-overview.md), du kan visa prestanda och hälsa för din infrastruktur för Linux-behållare på ett ögonblick och undersöka problem snabbt. Telemetri lagras i Log Analytics-arbetsytan och integrerat i Azure-portalen där du kan utforska, filtrera och segmentera aggregerade data med instrumentpaneler för att hålla ett öga på belastning, prestanda och hälsa.  

För behållare som körs utanför den värdbaserade tjänsten i Azure Kubernetes, Log Analytics [Windows-och Docker-behållare](../../azure-monitor/insights/containers.md) hjälper dig att visa och hantera dina Windows- och Docker-behållare-värdar. Från Log Analytics-arbetsytan, kan du visa inventeringsinformation, prestanda och händelser från noderna och behållarna i miljön. Du kan visa detaljerad granskning av information som visar kommandon som används med behållare och du kan felsöka behållare genom att visa och söka i centraliserade loggar utan att fjärransluta till Docker- eller Windows-värdar.

För att uppnå komplett eller slutpunkt till slutpunkt övervakning av programmet, bör eventuella beroenden om den är en Azure eller en lokal resurs, övervakas med Azure Monitor eller Log Analytics.  Programlagret ska inkluderas för att lägga till ett extra lager av hälsotillstånd i sin både på nivån plattformens och programmets med Application Insights. På nivån plattform är Application Insights SDK: er för [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Mikrotjänstprogram, det finns stöd för [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../application-insights/app-insights-nodejs-quick-start.md), [.Net](../../azure-monitor/app/asp-net.md), [.Net Core](../../azure-monitor/app/asp-net-core.md), samt ett antal andra [språk/ramverk](../../application-insights/app-insights-platforms.md). 

I annat fall problem går oidentifierade som kan påverka tillgängligheten för programmet och servicenivåavtal uppfylls inte.  
