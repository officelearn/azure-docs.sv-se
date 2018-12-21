---
title: Vad är Azure Container Instances?
description: Tjänsten Azure Container Instances är det snabbaste och enklaste sättet att köra isolerade behållare i Azure, utan att behöva hantera virtuella datorer och utan att behöva använda en initierare på högre nivå.
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 11/30/2018
ms.author: seanmck
ms.custom: seodec18, mvc
ms.openlocfilehash: ba454965ff2bb78ebe526e71d9280200b1f4b08b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187202"
---
# <a name="what-is-azure-container-instances"></a>Vad är Azure Container Instances?

Containrar är på väg att bli det bästa sättet att paketera, distribuera och hantera molnprogram. Azure Container Instances erbjuder det snabbaste och enklaste sättet att köra en behållare i Azure, utan att behöva hantera några virtuella datorer och utan att behöva använda en tjänst på högre nivå.

Azure Container Instances är en bra lösning för alla scenarier som kan fungera i isolerade behållare, däribland enkla program, automatisering av uppgifter och att skapa jobb. För scenarier där du behöver fullständig containerorkestrering, inklusive tjänstidentifiering för flera containrar, automatisk skalning och koordinerade programuppgraderingar rekommenderar vi [Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Snabba starttider

Med containrar får du betydande startfördelar jämfört med virtuella datorer. Azure Container Instances kan starta en behållare i Azure på några sekunder utan att några virtuella datorer behöver etableras eller hanteras.

## <a name="public-ip-connectivity-and-dns-name"></a>Offentlig IP-anslutning och DNS-namn

Med Azure Container Instances kan du exponera dina behållare direkt för Internet med en IP-adress och ett fullständigt domännamn (FQDN). När du skapar en behållarinstans kan du ange en anpassad DNS-namnsetikett, så att programmet kan nås på *customlabel*. *azureregion*. azurecontainer.io.

## <a name="hypervisor-level-security"></a>Säkerhet på hypervisornivå

Tidigare har containrar erbjudit isolering av programberoenden och resursstyrning men har inte ansetts vara tillräckligt strikta för fientlig användning med flera innehavare. Azure Container Instances garanterar att ditt program är lika isolerat i en behållare som i en virtuell dator.

## <a name="custom-sizes"></a>Anpassade storlekar

Containrar är vanligtvis optimerade för att endast köra ett program, men de specifika behoven för dessa program kan skilja sig åt avsevärt. Azure Container Instances erbjuder optimal användning eftersom det tillåter exakta specifikationer av CPU-kärnor och minne. Du betalar för vad du behöver och faktureras per sekund, så att du kan finjustera dina utgifter utifrån dina faktiska behov.

För beräkningsintensiva jobb, till exempel maskininlärning, kan Azure Container Instances schemalägga Linux-containrar att använda NVIDIA Tesla [GPU-resurser](container-instances-gpu.md) (förhandsversion).

## <a name="persistent-storage"></a>Beständig lagring

Vi erbjuder direkt [montering av Azure Files-resurser](container-instances-mounting-azure-files-volume.md) för att hämta och bevara tillstånd med Azure Container Instances.

## <a name="linux-and-windows-containers"></a>Linux- och Windows-containrar

Azure Container Instances kan schemalägga både Windows- och Linux-behållare med samma API. Ange typ av operativsystem när du skapar dina [containergrupper](container-instances-container-groups.md).

Vissa funktioner är för närvarande begränsade till Linux-container. Medan vi arbetar med att förse Windows-behållare med funktionsparitet kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

Azure Container Instances stöder Windows-bilder baserat på LTSC-versioner (Long-Term Servicing Channel). SAC-versioner (halvårskanal) för Windows såsom 1709 och 1803 stöds inte.

## <a name="co-scheduled-groups"></a>Samordna schemalagda grupper

Azure Container Instances stöder schemaläggning av [grupper med flera behållare](container-instances-container-groups.md) som delar en värddator, lokalt nätverk, lagring och livscykel. Det gör att du kan kombinera din huvudprogramcontainer med andra stödrollscontainrar, t.ex. sidecar-filer för loggning.

## <a name="virtual-network-deployment-preview"></a>Distribution av virtuellt nätverk (förhandsversion)

Den här funktionen i Azure Container Instances, som för närvarande är en förhandsversion, möjliggör [distribution av containerinstanser i ett virtuellt Azure-nätverk](container-instances-vnet.md). Genom att distribuera containerinstanser till ett undernät i ditt virtuella nätverk kan de kommunicera säkert med andra resurser i det virtuella nätverket, inklusive sådana som finns lokalt (via [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)).

> [!IMPORTANT]
> Vissa funktioner i Azure Container Instances är i förhandsversion och vissa [begränsningar gäller](container-instances-vnet.md#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionerna kan ändras innan de är allmänt tillgängliga (GA).

## <a name="next-steps"></a>Nästa steg

Försök att distribuera en container till Azure med ett enda kommando med hjälp av vår snabbstartsguide:

> [!div class="nextstepaction"]
> [Azure Container Instances Snabbstart](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
