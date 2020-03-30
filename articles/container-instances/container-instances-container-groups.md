---
title: Introduktion till behållargrupper
description: Lär dig mer om behållargrupper i Azure Container Instances, en samling instanser som delar en livscykel och resurser som lagring och nätverk
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247220"
---
# <a name="container-groups-in-azure-container-instances"></a>Behållargrupper i Azure Container Instances

Resursen på den översta nivån i Azure Container Instances är *behållargruppen*. I den här artikeln beskrivs vilka behållargrupper som är och vilka typer av scenarier de aktiverar.

## <a name="what-is-a-container-group"></a>Vad är en behållargrupp?

En behållargrupp är en samling behållare som schemaläggs på samma värddator. Behållarna i en behållargrupp delar en livscykel, resurser, lokala nätverk och lagringsvolymer. Det liknar i konceptet till en *pod* i [Kubernetes][kubernetes-pod].

I följande diagram visas ett exempel på en behållargrupp som innehåller flera behållare:

![Diagram över behållargrupper][container-groups-example]

Det här exemplet behållaregrupp:

* Schemaläggs på en enda värddator.
* Tilldelas en DNS-namnetikett.
* Exponerar en enda offentlig IP-adress med en exponerad port.
* Består av två behållare. En container lyssnar på port 80, medan den andra lyssnar på port 5000.
* Innehåller två Azure-filresurser som volymfästen och varje behållare monterar en av resurserna lokalt.

> [!NOTE]
> Multi-container grupper stöder för närvarande endast Linux-behållare. För Windows-behållare stöder Azure Container Instances endast distribution av en enskild behållarinstans. Medan vi arbetar för att få alla funktioner till Windows-behållare, kan du hitta aktuella plattformsskillnader i tjänsten [Översikt](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribution

Här är två vanliga sätt att distribuera en grupp med flera behållare: använd en [Resource Manager-mall][resource-manager template] eller en [YAML-fil][yaml-file]. En Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure-tjänstresurser (till exempel en [Azure Files-resurs)][azure-files]när du distribuerar behållarinstanserna. På grund av YAML-formatets mer koncisa natur rekommenderas en YAML-fil när distributionen endast innehåller behållarinstanser. Mer information om egenskaper som du kan ange finns i [referensdokumentationen för Resurshanterarens mallreferens](/azure/templates/microsoft.containerinstance/containergroups) eller [YAML-referens.](container-instances-reference-yaml.md)

Om du vill bevara konfigurationen av en behållargrupp kan du exportera konfigurationen till en YAML-fil med hjälp av Azure CLI-kommandot [az container export][az-container-export]. Med Export kan du lagra konfigurationerna för behållargruppen i versionskontrollen för "konfiguration som kod". Du kan också använda den exporterade filen som utgångspunkt när du utvecklar en ny konfiguration i YAML.



## <a name="resource-allocation"></a>Resursallokeringen

Azure Container Instances allokerar resurser som processorer, minne och eventuellt [GPU :er][gpus] (förhandsversion) till en grupp med flera behållare genom att lägga till [resursbegäranden för][resource-requests] instanserna i gruppen. Om du tar CPU-resurser som exempel, om du skapar en behållargrupp med två behållarinstanser, som var och en begär 1 CPU, tilldelas behållargruppen två processorer.

### <a name="resource-usage-by-container-instances"></a>Resursanvändning efter behållarinstanser

Varje behållarinstans i en grupp allokeras de resurser som anges i dess resursbegäran. De maximala resurser som används av en behållarinstans i en grupp kan dock vara olika om du konfigurerar egenskapen valfri [resursgräns.][resource-limits] Resursgränsen för en behållarinstans måste vara större än eller lika med egenskapen obligatorisk [resursbegäran.][resource-requests]

* Om du inte anger en resursgräns är behållarinstansens maximala resursanvändning densamma som resursbegäran.

* Om du anger en gräns för en behållarinstans kan instansens maximala användning vara större än begäran, upp till den gräns du anger. På motsvarande sätt kan resursanvändningen av andra behållarinstanser i gruppen minska. Den maximala resursgränsen som du kan ange för en behållarinstans är det totala antalet resurser som allokerats till gruppen.
    
I en grupp med två behållarinstanser som var och en begär 1 PROCESSOR kan en av dina behållare till exempel köra en arbetsbelastning som kräver att fler processorer körs än den andra.

I det här fallet kan du ange en resursgräns på 2 processorer för behållarinstansen. Med den här konfigurationen kan behållarinstansen använda upp till hela 2 processorer om det är tillgängligt.

### <a name="minimum-and-maximum-allocation"></a>Minsta och högsta tilldelning

* Allokera **minst** 1 CPU och 1 GB minne till en behållargrupp. Enskilda behållarinstanser inom en grupp kan etableras med mindre än 1 CPU och 1 GB minne. 

* För **maximalt** antal resurser i en behållargrupp finns i [resurstillgängligheten][region-availability] för Azure Container Instances i distributionsregionen.

## <a name="networking"></a>Nätverk

Behållargrupper kan dela en extern IP-adress, en eller flera portar på IP-adressen och en DNS-etikett med ett fullständigt kvalificerat domännamn (FQDN). Om du vill att externa klienter ska kunna nå en behållare i gruppen måste du exponera porten på IP-adressen och från behållaren. Eftersom behållare i gruppen delar ett portnamnområde stöds inte portmappning. En behållargrupps IP-adress och FQDN frisläpps när behållargruppen tas bort. 

Inom en behållargrupp kan behållarinstanser nå varandra via localhost på valfri port, även om dessa portar inte exponeras externt på gruppens IP-adress eller från behållaren.

Du kan också distribuera behållargrupper till ett [virtuellt Azure-nätverk][virtual-network] så att behållare kan kommunicera säkert med andra resurser i det virtuella nätverket.

## <a name="storage"></a>Lagring

Du kan ange externa volymer som ska monteras i en behållargrupp. Volymer som stöds inkluderar:
* [Azure-filresurs][azure-files]
* [Hemlighet][secret]
* [Tom katalog][empty-directory]
* [Klonad git repo][volume-gitrepo]

Du kan mappa dessa volymer till specifika sökvägar i de enskilda behållarna i en grupp. 

## <a name="common-scenarios"></a>Vanliga scenarier

Grupper med flera behållare är användbara i de fall där du vill dela upp en enda funktionell uppgift i ett litet antal behållaravbildningar. Dessa avbildningar kan sedan levereras av olika team och ha separata resurskrav.

Exempel på användning kan vara:

* En behållare som betjänar ett webbprogram och en behållare som hämtar det senaste innehållet från källkontrollen.
* En programbehållare och en loggningsbehållare. Loggningsbehållaren samlar in loggar och mått som utdata från huvudprogrammet och skriver dem till långsiktig lagring.
* En programbehållare och en övervakningsbehållare. Övervakningsbehållaren gör regelbundet en begäran till programmet för att säkerställa att den körs och svarar korrekt och väcker en avisering om den inte är det.
* En front-end behållare och en back-end behållare. Klientdelen kan fungera som ett webbprogram, där serverdelen kör en tjänst för att hämta data. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en behållare med flera behållare med en Azure Resource Manager-mall:

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
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
