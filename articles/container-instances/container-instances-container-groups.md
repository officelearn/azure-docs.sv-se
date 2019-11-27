---
title: Introduktion till behållar grupper
description: Lär dig mer om behållar grupper i Azure Container Instances, en samling instanser som delar en livs cykel och resurser, till exempel lagring och nätverk
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 9fbf9fea7da0896ee6c0e248d18e18d52798fbd7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482112"
---
# <a name="container-groups-in-azure-container-instances"></a>Behållar grupper i Azure Container Instances

Resursen på den översta nivån i Azure Container Instances är *behållar gruppen*. I den här artikeln beskrivs vilka behållar grupper som är och vilka typer av scenarier de aktiverar.

## <a name="what-is-a-container-group"></a>Vad är en behållar grupp?

En behållar grupp är en samling behållare som är schemalagda på samma värddator. Behållarna i en behållar grupp delar en livs cykel, resurser, lokalt nätverk och lagrings volymer. Det liknar begreppet *Pod* i [Kubernetes][kubernetes-pod].

Följande diagram visar ett exempel på en behållar grupp som innehåller flera behållare:

![Diagram över container grupper][container-groups-example]

Den här exempel behållar gruppen:

* Har schemalagts på en dator med en enda värd.
* Har tilldelats en DNS-benämning.
* Exponerar en enskild offentlig IP-adress med en exponerad port.
* Består av två behållare. En behållare lyssnar på port 80, medan den andra lyssnar på port 5000.
* Innehåller två Azure-filresurser som volym monteringar och varje behållare monterar en av resurserna lokalt.

> [!NOTE]
> Grupper med flera behållare stöder för närvarande endast Linux-behållare. För Windows-behållare stöder Azure Container Instances endast distribution av en enda instans. Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i tjänst [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribution

Här följer två vanliga sätt att distribuera en grupp med flera behållare: Använd en [Resource Manager-mall][resource-manager template] eller en [yaml-fil][yaml-file]. En Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure-tjänst resurser (till exempel en [Azure Files resurs][azure-files]) när du distribuerar behållar instanserna. På grund av YAML-formatets mer koncisa typ rekommenderas en YAML-fil när distributionen endast innehåller behållar instanser. Mer information om egenskaper som du kan ange finns i [referens dokumentationen för Resource Manager-mallen](/azure/templates/microsoft.containerinstance/containergroups) eller [yaml](container-instances-reference-yaml.md) .

Om du vill bevara en behållar grupps konfiguration kan du exportera konfigurationen till en YAML-fil med hjälp av Azure CLI-kommandot [AZ container export][az-container-export]. Med export kan du lagra konfigurationerna för behållar grupper i versions kontroll för "konfiguration som kod". Du kan också använda den exporterade filen som en start punkt när du utvecklar en ny konfiguration i YAML.



## <a name="resource-allocation"></a>Resursallokering

Azure Container Instances allokerar resurser som processorer, minne och alternativt [GPU][gpus] (för hands version) till en behållar grupp genom att lägga till [resurs begär Anden][resource-requests] för instanserna i gruppen. Att ta processor resurser som exempel, om du skapar en behållar grupp med två instanser, varje begäran 1 processor, allokeras behållar gruppen 2 processorer.

### <a name="resource-usage-by-instances"></a>Resursanvändning efter instanser

Varje behållar instans tilldelas resurserna som anges i resurs förfrågan. Resurs användningen av en behållar instans i en grupp beror dock på hur du konfigurerar den valfria [resurs begränsnings][resource-limits] egenskapen.

* Om du inte anger en resurs gräns, är instansens maximala resursanvändning samma som resurs förfrågan.

* Om du anger en resurs gräns för en instans kan du justera instansens resursanvändning för arbets belastningen, antingen minska eller öka användningen i förhållande till resurs förfrågan. Den maximala resurs gränsen du kan ange är det totala antalet resurser som allokeras till gruppen.
    
    I en grupp med till exempel två instanser som begär 1 processor kan en av dina behållare köra en arbets belastning som kräver fler processorer för att köras än den andra.

    I det här scenariot kan du ange en resurs gräns på 0,5 CPU för en instans och en gräns på 2 processorer för den andra. Den här konfigurationen begränsar den första behållarens resursanvändning till 0,5 CPU, så att den andra behållaren kan använda upp till hela 2 CPU: n om den är tillgänglig.

Mer information finns i egenskapen [ResourceRequirements][resource-requirements] i behållar grupper REST API.

### <a name="minimum-and-maximum-allocation"></a>Lägsta och högsta allokering

* Allokera **minst** 1 processor och 1 GB minne till en behållar grupp. Enskilda behållar instanser i en grupp kan tillhandahållas med mindre än 1 processor och 1 GB minne. 

* För **maximala** resurser i en behållar grupp, se [resurs tillgänglighet][region-availability] för Azure Container instances i distributions regionen.

## <a name="networking"></a>Nätverk

Behållar grupper delar en IP-adress och en port namn rymd på den IP-adressen. Om du vill att externa klienter ska kunna komma åt en behållare i gruppen måste du exponera porten på IP-adressen och från behållaren. Eftersom behållare i gruppen delar ett port namn område, stöds port mappning inte. Behållare i en grupp kan komma åt varandra via localhost på de portar som de har exponerat, även om dessa portar inte exponeras externt på gruppens IP-adress.

Du kan också distribuera behållar grupper till ett [virtuellt Azure-nätverk][virtual-network] (för hands version) för att tillåta att behållare kommunicerar säkert med andra resurser i det virtuella nätverket.

## <a name="storage"></a>Storage

Du kan ange externa volymer som ska monteras i en behållar grupp. Du kan mappa dessa volymer till specifika sökvägar inom de enskilda behållarna i en grupp.

## <a name="common-scenarios"></a>Vanliga scenarier

Grupper med flera behållare är användbara i de fall där du vill dela upp en enda funktions aktivitet i ett litet antal behållar avbildningar. Dessa bilder kan sedan levereras av olika team och ha separata resurs krav.

Exempel på användning kan vara:

* En behållare som hanterar ett webb program och en behållare hämtar det senaste innehållet från käll kontroll.
* En program behållare och en loggnings behållare. Loggnings behållaren samlar in loggar och mät resultat från det huvudsakliga programmet och skriver dem till långsiktig lagring.
* En program behållare och en övervaknings behållare. Övervaknings behållaren skickar regelbundet en begäran till programmet för att säkerställa att den körs och svarar korrekt, och genererar en avisering om den inte är det.
* En klient dels behållare och en backend-behållare. Klient delen kan betjäna ett webb program, med Server delen som kör en tjänst för att hämta data. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en container grupp med flera behållare med en Azure Resource Manager-mall:

> [!div class="nextstepaction"]
> [Distribuera en behållar grupp][resource-manager template]

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
