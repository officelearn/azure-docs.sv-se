---
title: Azure Service Fabric Monitoring Partners | Microsoft Docs
description: Lär dig hur du övervakar Azure Service Fabric med partner övervakningslösningar
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: f7bf5d521f4bcb5672ff1d710a08bed2e0872545
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974411"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric Monitoring partner

Den här artikeln visar hur en kan övervaka sina Service Fabric-program, kluster och infrastruktur med en handfull partnerlösningar. Vi har arbetat med var och en av partners nedan för att skapa integrerade erbjudanden för Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Vår integrering med Dynatrace tillhandahåller många utanför rutan funktionerna för att övervaka dina Service Fabric-kluster. Installera Dynatrace OneAgent på dina skalningsuppsättningsinstanser ger du prestandaräknare och en topologi för distributionen av Service Fabric App-nivå. Dynatrace är också ett bra alternativ för lokal övervakning. Kolla in flera av de funktioner som visas i den [meddelandet](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) och [instruktioner](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) att aktivera Dynatrace på ditt kluster. 

## <a name="datadog"></a>Datadog

Datadog har ett tillägg för VMSS för både Windows och Linux-instanser. Med Datadog kan du samla in Windows-händelseloggar och därmed samla in händelser för Service Fabric-plattformen på Windows. Läs anvisningarna om hur du skickar diagnostikdata till Datadog [här](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Service Fabric-integrering med AppDynamics är på programnivå. Genom att uppdatera miljövariabler och använder appen Dynamics NuGets, kan du skicka programtelemetri till AppDynamics. Ser du följande [instruktioner](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) för hur du integrerar dina .NET Service Fabric-program med AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic är ett annat verktyg för hantering av programprestanda som integrerar väl med Service Fabric-program. Du kan installera den nya Relic NuGet-paket och lägga till specifika miljövariabler i ditt manifest-filer att skicka din programtelemetri till New Relic. Kolla in dessa [instruktioner](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) att aktivera New Relic telemetri för .NET Service Fabric-program.

## <a name="elk"></a>ELK 

ELK-stacken är en uppsättning tekniker för öppen källkod: Elasticsearch, Logstash och Kibana. Genom att använda dessa i kombination, kan du samla in, lagra och analysera data för Service Fabric övervakning och diagnostik. Vi har en självstudiekurs om hur du gör detta med Service Fabric interna Java-program [här](service-fabric-tutorial-java-elk.md). 


## <a name="next-steps"></a>Nästa steg

* Hämta en [översikt över övervakning och diagnostik](service-fabric-diagnostics-overview.md) i Service Fabric
* Lär dig hur du [diagnostisera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md) med verktyg för vår första part
