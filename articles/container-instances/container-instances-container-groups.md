---
title: Azure Behållarinstanser behållargrupper
description: Förstå hur behållargrupper fungerar i Azure Container instanser
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: cb3d8c27a82c7dfc5fd71c1c7d589e81890e5cfb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163307"
---
# <a name="container-groups-in-azure-container-instances"></a>Behållargrupper i Azure Container instanser

Resursen på den översta nivån i Azure Container instanser är den *behållargruppen*. Den här artikeln beskriver behållargrupper är och vilka typer av scenarier som de aktiverar.

## <a name="how-a-container-group-works"></a>Så här fungerar en behållare grupp

En behållare grupp är en samling av behållare som få schemalagda på samma värddator. Behållare i en behållare grupp dela en livscykel, lokala nätverk och lagringsvolymer. Det är liknande koncept som i en *baljor* i [Kubernetes] [ kubernetes-pod] och [DC/OS][dcos-pod].

Följande diagram visar ett exempel på en behållare grupp som innehåller flera behållare:

![Behållaren grupper diagram][container-groups-example]

Det här exemplet behållaren grupp:

* Schemaläggs på en enda värddator.
* En DNS-Namnetiketten är tilldelad.
* Visar en offentlig IP-adress, med en exponerade port.
* Består av två behållare. En behållare lyssnar på port 80, medan andra lyssnar på port 5000.
* Innehåller två Azure filresurser som volym monteringar och varje behållare monterar en resurs lokalt.

> [!NOTE]
> Flera behållare grupper är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="deployment"></a>Distribution

Behållaren *grupper* har en minsta resursallokering 1 vCPU och 1 GB minne. Enskilda *behållare* i en behållare som gruppen kan etableras med mindre än 1 vCPU och 1 GB minne. Distribution av resurser kan anpassas till flera behållare inom de gränser som anges på behållaren gruppnivå i en behållare i grupp. Till exempel två behållare varje med 0,5 vCPU som finns i en behållare-grupp som har allokerats 1 vCPU.

## <a name="networking"></a>Nätverk

Behållargrupper delar en IP-adress och ett namnområde för port på att IP-adress. Om du vill aktivera externa klienter ska nå en behållare i gruppen, måste du exponera port på IP-adressen och från behållaren. Eftersom behållare i gruppen delar port namnområde stöds portmappning inte. Behållare i en grupp kan du nå en varandra via localhost på de portar som de har exponerade, även om dessa portar inte är tillgängliga externt på gruppens IP-adress.

## <a name="storage"></a>Lagring

Du kan ange externa volymer att montera i en grupp i behållaren. Du kan mappa volymerna till specifika sökvägar i enskilda behållare i en grupp.

## <a name="common-scenarios"></a>Vanliga scenarier

Flera behållare grupper är användbart i fall där du vill dela en enda funktionella aktivitet i ett litet antal behållare bilder. Dessa avbildningar kan sedan levereras med olika team och ha separata resurskraven.

Exempel på användning kan vara:

* En behållare för program och en behållare för loggning. Behållaren loggning samlar in loggar och mått utdata av de huvudsakliga programmet och skriver dem till långsiktig lagring.
* En behållare för program och en behållare för övervakning. Behållaren övervakning skickar med jämna mellanrum en begäran till programmet så att den körs och svara på rätt sätt och aktiverar en avisering om det inte.
* En behållare som betjänar ett webbprogram och en behållare dra det senaste innehållet från källkontroll.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en behållare för flera behållare grupp med en Azure Resource Manager-mall:

> [!div class="nextstepaction"]
> [Distribuera en behållare grupp](container-instances-multi-container-group.md)

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
