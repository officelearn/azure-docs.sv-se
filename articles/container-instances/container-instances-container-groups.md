---
title: Grupper med Azure Container Instances-behållare
description: Förstå hur flera behållargrupper fungerar i Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4bbea8acd447a731cf5c56f9876baf9183735ea
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785001"
---
# <a name="container-groups-in-azure-container-instances"></a>Behållargrupper i Azure Container Instances

Översta resursen i Azure Container Instances är den *behållargruppen*. Den här artikeln beskriver vilka behållargrupper är och vilka typer av scenarier som de gör.

## <a name="how-a-container-group-works"></a>Så här fungerar en behållargrupp

En behållargrupp är en uppsättning behållare som kan få schemalagda på samma värddator. Behållare i en behållargrupp delar en livscykel, resurser, nätverket och lagringsvolymer. Det påminner om att en *pod* i [Kubernetes][kubernetes-pod].

I följande diagram visas ett exempel på en behållargrupp som innehåller flera behållare:

![Behållaren grupper diagram][container-groups-example]

Det här exemplet behållargruppen:

* Har schemalagts på en enda värddator.
* Är tilldelad en DNS-namnetikett.
* Exponerar en offentlig IP-adress, med en exponerade port.
* Består av två behållare. En behållare lyssnar på port 80, samtidigt som de andra lyssnar på port 5000.
* Omfattar två Azure-filresurser som volym monterar och varje behållare monterar en resurs lokalt.

> [!NOTE]
> Grupper med flera behållare stöder för närvarande endast Linux-behållare. För Windows-behållare, Azure Container Instances endast har stöd för distribution av en enda instans. När vi arbetar för att göra alla funktioner till Windows-behållare kan du hitta nuvarande skillnaderna i tjänsten [översikt](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribution

Här är två vanliga sätt att distribuera en grupp med flera behållare: använda en [Resource Manager-mall] [ resource-manager template] eller en [YAML-fil][yaml-file]. Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure-tjänstresurser (till exempel en [Azure Files dela][azure-files]) när du distribuerar behållarinstanserna. På grund av YAML-format kortare natur rekommenderas en YAML-fil när distributionen omfattar endast behållarinstanser.

För att bevara en behållargrupp konfiguration, kan du exportera konfigurationen till en YAML-fil med hjälp av Azure CLI-kommando [az container export][az-container-export]. Exportera kan du lagra dina behållare konfigurationer i versionskontroll för ”konfiguration som kod”. Eller Använd den exporterade filen som en startpunkt när du utvecklar en ny konfiguration i YAML.

## <a name="resource-allocation"></a>Resursallokering

Azure Container Instances allokerar resurser, t.ex processorer, minne, och eventuellt [GPU: er] [ gpus] (förhandsversion) och en behållargrupp genom att lägga till den [resursbegäranden] [ resource-requests] av instanserna i gruppen. Med CPU-resurser som till exempel om du skapar en behållargrupp med två instanser varje begärande 1 CPU och sedan behållargruppen allokeras 2 processorer.

De maximala resurserna som är tillgängliga för en behållargrupp beror på den [Azure-region] [ region-availability] används för distributionen.

### <a name="container-resource-requests-and-limits"></a>Behållaren resursbegäranden och begränsningar

* Som standard delar behållarinstanserna i en grupp de begärda resurserna i gruppen. I en grupp med två instanser med varje begärande 1 CPU, gruppen som helhet har åtkomst till 2 processorer. Varje instans kan använda upp till 2 processorer och instanserna kan tävla om processorresurser när de körs.

* För att begränsa användning av en instans i en grupp, om du vill ange en [resursgränsen] [ resource-limits] för instansen. I en grupp med två instanser begär 1 CPU, en av dina behållare kan kräva mer CPU: er att köra än den andra.

  I det här scenariot kan du ange resource maxgränsen 0,5 CPU för en instans och en gräns på 2 processorer för andra. Den här konfigurationen begränsar resursanvändningen för den första behållaren till 0,5 CPU, så att andra behållaren använda upp till fullständig 2 processorer om det är tillgängligt.

Mer information finns i den [ResourceRequirements] [ resource-requirements] -egenskapen i behållaren grupperar REST API.

### <a name="minimum-and-maximum-allocation"></a>Lägsta och högsta allokering

* Allokera en **minsta** 1 CPU och 1 GB minne till en behållargrupp. Enskild behållarinstanser i en grupp kan etableras med mindre än 1 processor- och 1 GB minne. 

* För den **maximala** resurser i en behållargrupp finns i [resurstillgänglighet] [aci region-tillgänglighet] för Azure Container Instances i distributionsregionen.

## <a name="networking"></a>Nätverk

Behållargrupper delar en IP-adress och en port-namnområde på IP-adress. Om du vill aktivera externa klienter ska nå en behållare i gruppen, måste du exponera port på IP-adress och från behållaren. Eftersom behållare i gruppen som delar en port-namnrymd, stöds inte portmappning. Behållare i en grupp kan du nå en varandra via localhost på de portar som de har visas även om inte dessa portar är gjorda tillgängliga externt på gruppens IP-adress.

Du kan också distribuera behållargrupper i en [Azure-nätverk] [ virtual-network] (förhandsversion) för att tillåta behållare för att kommunicera säkert med andra resurser i det virtuella nätverket.

## <a name="storage"></a>Storage

Du kan ange externa volymer ska monteras i en behållargrupp. Du kan mappa volymerna till specifika sökvägar i de enskilda behållarna i en grupp.

## <a name="common-scenarios"></a>Vanliga scenarier

Grupper med flera behållare är användbart i fall där du vill dela upp en funktionell aktivitet i ett litet antal behållaravbildningar. Dessa avbildningar kan sedan levereras av olika team och har separat resurskrav.

Exempel på användning kan omfatta:

* En behållare som betjänar ett webbprogram och en behållare som hämtar det senaste innehållet från källkontroll.
* En programbehållare och en behållare för loggning. Behållaren loggning samlar in loggar och mått utdata med huvudprogrammet och skriver dem till långsiktig lagring.
* En programbehållare och en övervakning behållare. Behållaren övervakning gör regelbundet en begäran till programmet så att den körs och svara på rätt sätt och aktiverar en avisering om den inte.
* En frontend-behållare och en backend-behållare. Klientdelen kan komma att hantera ett webbprogram med backend-servern som kör en tjänst för att hämta data. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en behållargrupp med flera behållare med en Azure Resource Manager-mall:

> [!div class="nextstepaction"]
> [Distribuera en behållargrupp][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
