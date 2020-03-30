---
title: Azure Service-infrastrukturövervakningspartner
description: Lär dig hur du övervakar Azure Service Fabric-program, kluster och infrastruktur med partnerövervakningslösningar.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645726"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service-infrastrukturövervakningspartner

Den här artikeln illustrerar hur man kan övervaka sina Service Fabric-program, kluster och infrastruktur med en handfull partnerlösningar. Vi har arbetat med var och en av partnerna nedan för att skapa integrerade erbjudanden för Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Vår integrering med Dynatrace innehåller många funktioner som inte finns för att övervaka dina Service Fabric-kluster. Genom att installera Dynatrace OneAgent på dina VMSS-instanser får du prestandaräknare och en topologi för distributionen av service fabric ner till appnivå. Dynatrace är också ett utmärkt val för lokal övervakning. Kolla in fler av de funktioner som anges i [meddelandet](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) och [instruktioner](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) för att aktivera Dynatrace i klustret. 

## <a name="datadog"></a>Datadog (datahund)

Datadog har ett tillägg för VMSS för både Windows- och Linux-instanser. Med Datadog kan du samla in Windows-händelseloggar och därmed samla in plattformshändelser för Service Fabric i Windows. Kolla in instruktionerna om hur du skickar dina diagnostikdata till Datadog [här](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Service Fabric-integreringen med AppDynamics är på programnivå. Genom att uppdatera miljövariabler och använda App Dynamics NuGets kan du skicka programtelemetri till AppDynamics. Se de här [anvisningarna](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) för hur du integrerar dina .NET Service Fabric-program med AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic är ett annat programprestandahanteringsverktyg som integreras väl med Service Fabric-program. Du kan installera paketen Ny Relik NuGet och lägga till specifika miljövariabler i manifestfilerna för att skicka programelemetrin till Ny relik. Läs in de här [instruktionerna](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) för att aktivera ny reliktelemetri för dina .NET Service Fabric-program.

## <a name="elk"></a>Elk 

ELK-stacken är en samling tekniker med öppen källkod: Elasticsearch, Logstash och Kibana. Genom att använda dessa tekniker i kombination kan du samla in, lagra och analysera övervaknings- och diagnostikdata för serviceinfrastruktur. Vi har en handledning för hur man gör detta med Service Fabric infödda Java-program [här](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio (1990

Humio är en logginsamlingstjänst som kan samla in loggar från dina program och händelser från Service Fabric i molnet eller lokalt i realtid. Förutom live observerbarhet erbjuder Humio state-of-the-art analys och visualisering kapacitet för visning och insamling av information från din diagnostik. Humio har kostnadseffektiva prisplaner och är byggd för att skala samtidigt som den är lättad snabb hastighet. Den integreras direkt med Service Fabric-plattformshändelser och programtelemetri. Du kan läsa mer om Humio och Service Fabric integration [här](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Nästa steg

* Få en [översikt över övervakning och diagnostik](service-fabric-diagnostics-overview.md) i Service Fabric
* Läs om hur du [diagnostiserar vanliga scenarier](service-fabric-diagnostics-common-scenarios.md) med våra förstapartsverktyg
