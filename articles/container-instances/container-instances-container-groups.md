---
title: "Azure-behållaren instanser Behållargrupper"
description: "Förstå hur Behållargrupper fungerar i Azure Container instanser"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 25eab41c3f0c986bcce33123f86f4c9638b77191
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="container-groups-in-azure-container-instances"></a>Behållargrupper i Azure-Behållarinstanser

Resursen på den översta nivån i Azure Container instanser är en grupp i behållaren. Den här artikeln beskriver vad behållargrupper är och vilka typer av scenarier som de aktiverar.

## <a name="how-a-container-group-works"></a>Så här fungerar en behållare grupp

En behållare grupp är en samling av behållare som hämta schemaläggs på samma värddator och dela en livscykel, lokala nätverk och lagringsvolymer. Den liknar konceptet för en *baljor* i [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) och [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/).

Följande diagram visar ett exempel på en behållare grupp som innehåller flera behållare.

![Behållaren grupper exempel][container-groups-example]

Tänk på följande:

- Gruppen har schemalagts på en enda värddator.
- Gruppen Exponerar en offentlig IP-adress, med en exponerade port.
- Gruppen består av två behållare. En behållare lyssnar på port 80, medan andra lyssnar på port 5000.
- Gruppen innehåller två Azure-filresurser som volym monteringar och varje behållare monterar en resurs lokalt.

### <a name="networking"></a>Nätverk

Behållargrupper delar en IP-adress och ett namnområde för port på att IP-adress. Om du vill aktivera externa klienter ska nå en behållare i gruppen, måste du exponera port på IP-adressen och från behållaren. Eftersom behållare i gruppen delar port namnområde stöds portmappning inte. Behållare i en grupp kan du nå en varandra via localhost på de portar som de har exponerade, även om dessa portar inte är tillgängliga externt på gruppens IP-adress.

### <a name="storage"></a>Lagring

Du kan ange externa volymer att montera i en grupp i behållaren. Du kan mappa volymerna till specifika sökvägar i enskilda behållare i en grupp.

## <a name="common-scenarios"></a>Vanliga scenarier

Flera behållare grupper är användbart i fall där du vill dela upp en funktionell aktivitet i ett litet antal behållare bilder, som levereras med olika team och ha separata resurskraven. Exempel på användning kan vara:

- En behållare för program och en behållare för loggning. Behållaren loggning samlar in loggar och mått utdata av de huvudsakliga programmet och skriver dem till långsiktig lagring.
- Ett program och en behållare för övervakning. Behållaren övervakning skickar med jämna mellanrum en begäran till programmet så att den körs och svara på rätt sätt och aktiverar en avisering om det inte.
- En behållare som betjänar ett webbprogram och en behållare dra det senaste innehållet från källkontroll.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera flera behållargruppen](container-instances-multi-container-group.md) med en Azure Resource Manager-mall.

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png