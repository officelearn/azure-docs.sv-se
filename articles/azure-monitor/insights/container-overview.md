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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: ed5e3bdc1025b4b827d3b895bce95f6949c46e83
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715746"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Översikt över övervakning av behållare i Azure
Med Azure kan du effektivt övervaka och hantera dina arbetsbelastningar som distribuerats på Azure som kör Kubernetes eller Docker-behållare. Det är viktigt att förstå hur behållare med flera mikrotjänstprogram utför för att leverera en tillförlitlig tjänst i skala och övervakning planen. Den här artikeln innehåller en kort översikt över hanterings- och övervakningsfunktionerna i Azure för att förstå dem och som är lämplig baserat på dina krav.

Med hjälp av [Azure Monitor för behållare](container-insights-overview.md), du kan visa prestanda och hälsa för din infrastruktur för Linux-behållare på ett ögonblick och undersöka problem snabbt. Telemetri lagras i Log Analytics-arbetsytan och integrerat i Azure-portalen där du kan utforska, filtrera och segmentera aggregerade data med instrumentpaneler för att hålla ett öga på belastning, prestanda och hälsa.  

För behållare som körs utanför den värdbaserade tjänsten i Azure Kubernetes, Log Analytics [Windows-och Docker-behållare](../../log-analytics/log-analytics-containers.md) hjälper dig att visa och hantera dina Windows- och Docker-behållare-värdar. Från Log Analytics-arbetsytan, kan du visa inventeringsinformation, prestanda och händelser från noderna och behållarna i miljön. Du kan visa detaljerad granskning av information som visar kommandon som används med behållare och du kan felsöka behållare genom att visa och söka i centraliserade loggar utan att fjärransluta till Docker- eller Windows-värdar.

För att uppnå komplett eller slutpunkt till slutpunkt övervakning av programmet, bör eventuella beroenden om den är en Azure eller en lokal resurs, övervakas med Azure Monitor eller Log Analytics.  Programlagret ska inkluderas för att lägga till ett extra lager av hälsotillstånd i sin både på nivån plattformens och programmets med Application Insights. På nivån plattform är Application Insights SDK: er för [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Mikrotjänstprogram, det finns stöd för [Java](../../application-insights/app-insights-java-get-started.md), [Node.js](../../application-insights/app-insights-nodejs-quick-start.md), [.Net](../../application-insights/app-insights-asp-net.md), [.Net Core](../../application-insights/app-insights-asp-net-core.md), samt ett antal andra [språk/ramverk](../../application-insights/app-insights-platforms.md). 

I annat fall problem går oidentifierade som kan påverka tillgängligheten för programmet och servicenivåavtal uppfylls inte.  
