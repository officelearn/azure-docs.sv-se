---
title: "Azure Service Fabric-Händelseanalys med OMS | Microsoft Docs"
description: "Läs mer om visualisera och analysera händelser med hjälp av OMS för övervakning och diagnostik av Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 09542c0e7f628ca4fea00a6562c0b9525432c213
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Händelseanalys och visualisering med OMS

Operations Management Suite (OMS) är en uppsättning tjänster som underlättar övervakning och diagnostik för program och tjänster i molnet. För att få en mer detaljerad översikt över OMS och den erbjuder, läsa [vad är OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Logganalys och OMS-arbetsyta

Logganalys samlar in data från hanterade resurser, inklusive en Azure-lagring eller en agent och underhåller i en central databas. Data kan sedan användas för analys, varningar och visualisering eller ytterligare exporteras. Logganalys stöder händelser, prestandadata eller andra anpassade data.

När OMS konfigureras, kommer du ha tillgång till en specifik *OMS-arbetsytan*, från där data kan efterfrågas eller visualiseras i instrumentpaneler.

När data tas emot av logganalys OMS har flera *hanteringslösningar* som är färdigförpackade lösningar för att övervaka inkommande data, anpassas efter flera scenarier. Dessa inkluderar en *Service Fabric Analytics* lösning och en *behållare* lösning som är de två viktigaste som att diagnostik- och övervaka när du använder Service Fabric-kluster. Det finns flera andra program som är värda att utforska och OMS kan även användas för att skapa anpassade lösningar som du kan läsa mer om [här](../operations-management-suite/operations-management-suite-solutions.md). Varje lösning som du vill använda för ett kluster kan konfigureras i samma OMS-arbetsyta, tillsammans med logganalys. Arbetsytor Tillåt för anpassade instrumentpaneler och visualisering av data och ändringar av data som du vill samla in processen och analysera.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Ställa in en OMS-arbetsyta med Service Fabric Analytics lösning
Vi rekommenderar att du inkluderar Service Fabric-lösningen i OMS-arbetsyta - innehåller en instrumentpanel som visar olika inkommande loggkanaler från nivån plattform och program och ger möjlighet att fråga Service Fabric specifika loggar. Här är en relativt enkla Service Fabric-lösningen ser ut, med ett enda program som distribuerats på klustret:

![OMS SA lösning](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Se [konfigurera OMS logganalys](service-fabric-diagnostics-oms-setup.md) att komma igång med den här för klustret.

## <a name="using-the-oms-agent"></a>Med hjälp av OMS-Agent

Är det rekommenderat att använda EventFlow och BOMULLSTUSS som aggregering lösningar eftersom de tillåter en mer modulär metod för diagnostik och övervakning. Om du vill ändra dina utdata från EventFlow krävs till exempel ändras inte den faktiska instrumentation en enkel ändring i konfigurationsfilen. Om du vill investera i med hjälp av OMS logganalys bör du ställa in den [OMS-agent](../log-analytics/log-analytics-windows-agents.md). Du bör också använda OMS-agenten när du distribuerar behållare i klustret som beskrivs nedan. 

Öppna över [lägga till OMS-Agent till ett kluster](service-fabric-diagnostics-oms-agent.md) stegvisa instruktioner för detta.

Fördelar med detta är följande:

* Rikare data om prestanda räknare och mått på klientsidan
* Enkelt att konfigurera mått som samlas in från klustret och utan att behöva uppdatera din klusterkonfigurationen. Ändringar av inställningarna för agentens kan göras från OMS-portalen och agenten startas om automatiskt så att den matchar konfigurationen. Om du vill konfigurera OMS-agent för specifika prestandaräknare, gå till arbetsytan **Start > Inställningar > Data > Windows prestandaräknare** och välj de data som du vill visa insamlade
* Data visas snabbare än med lagras före tas upp av OMS / logganalys
* Övervakning av behållare är mycket enklare eftersom den kan hämta docker-loggar (stdout, stderr) och statistik (prestandamått på behållaren och nod-nivåer)

Den huvudsakliga ersättningen är att eftersom agenten ska distribueras på ditt kluster tillsammans med alla program, det kan finnas vissa påverkan på prestanda för ditt program i klustret.

## <a name="monitoring-containers"></a>Övervakning av behållare

När du distribuerar behållare till ett Service Fabric-kluster, rekommenderas det att klustret har konfigurerats med OMS-agent och att lösningen behållare har lagts till OMS-arbetsyta för att övervaka och diagnostik. Här är lösningen behållare ser ut i en arbetsyta:

![Grundläggande OMS-instrumentpanelen](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamlingen av flera behållare-specifika loggar som kan frågas i OMS eller användas för visualiserade nyckeltal. Log-typer som samlas in är:

* ContainerInventory: Visar information om behållarens plats, namn och bilder
* ContainerImageInventory: information om distribuerade bilder, inklusive ID eller storlekar
* ContainerLog: specifika felloggar, docker-loggar (stdout osv.) och andra transaktioner
* ContainerServiceLog: docker daemon kommandon som har körts
* Prestanda: prestandaräknare inklusive behållaren cpu, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna

[Övervaka behållare med OMS logganalys](service-fabric-diagnostics-oms-containers.md) beskriver de steg som krävs för att ställa in behållaren övervakning för klustret. Mer information om OMSS behållare lösning checka ut sina [dokumentationen](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Nästa steg

Utforska följande OMS-verktyg och alternativ för att anpassa en arbetsyta för dina behov:

* För lokala kluster erbjuder OMS Gateway (HTTP framåt Proxy) som kan användas för att skicka data till OMS. Läs mer om att [ansluta datorer utan Internetanslutning till OMS med OMS-Gateway](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys