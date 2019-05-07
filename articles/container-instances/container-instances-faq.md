---
title: Azure Container Instances – vanliga frågor och svar
description: Svar på vanliga frågor som rör tjänsten Azure Container Instances
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079022"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Vanliga frågor och svar om Azure Container Instances

Den här artikeln tar upp vanliga frågor och svar om Azure Container Instances.

## <a name="deployment"></a>Distribution

### <a name="how-large-can-my-container-image-be"></a>Hur stor kan min behållaravbildning vara?

Den maximala storleken för en distribuerbar behållaravbildning på Azure Container Instances är 15 GB. Du kan eventuellt att distribuera avbildningar från större beroende på exakt tillgängligheten för tillfället som du distribuerar, men det är inte säkert.

Storleken på din behållaravbildning påverkar hur lång tid det tar för att distribuera Allmänt du vill behålla dina behållaravbildningar så mycket som möjligt.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hur kan jag påskynda distributionen av min behållare?

Eftersom en av de viktigaste faktorerna för distributionstid avbildningens storlek, leta efter sätt att minska storleken. Ta bort lager som du inte behöver, eller minska storleken på lager i avbildningen (genom att välja en ljusare OS-basavbildning). Till exempel om du kör Linux-behållare, Överväg att använda Alpine som din basavbildningen i stället för en fullständig Ubuntu-Server. På samma sätt för Windows-behållare, använda en grundläggande Nano Server-avbildning om det är möjligt. 

Du bör också kontrollera listan över cachelagrad avbildningar i Azure-Behållaravbildningar, tillgängligt via den [lista cachelagras avbildningar](/rest/api/container-instances/listcachedimages) API. Du kanske kan byta ut ett lager för avbildning för en cachelagrad avbildning. 

Se fler [detaljerad vägledning](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) på minskar starttiden för behållaren.

### <a name="what-windows-base-os-images-are-supported"></a>Vilka Windows-grundläggande OS-avbildningar stöds?

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 Källavbildningen

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Windows-bilder baserat på Halvårskanal version 1709 eller 1803 stöds inte.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 och klienten Källavbildningen (förhandsversion)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Vilka .NET eller .NET Core bildlager i min behållare ska jag använda? 

Använd en liten bild som uppfyller dina krav. För Linux, kan du använda en *runtime-alpine* .NET Core-avbildningen, som har stöd sedan versionen av .NET Core 2.1. För Windows, om du använder det fullständiga .NET Framework måste du använda en Windows Server Core-avbildning (endast runtime-avbildning, till exempel *4.7.2-windowsservercore-ltsc2016*). Endast Runtime-avbildningar är mindre men har inte stöd för arbetsbelastningar som kräver .NET SDK.

## <a name="availability-and-quotas"></a>Tillgänglighet och kvoter

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hur många kärnor och minne bör jag allokera för Mina behållare eller behållargruppen?

Detta beror på din arbetsbelastning. Börja i liten skala och testa prestanda och se hur dina behållare göra. [Övervaka Resursanvändning som CPU och minne](container-instances-monitor.md), och Lägg sedan till kärnor eller minne baserat på vilken typ av processer som du distribuerar i behållaren. 

Kontrollera också att kontrollera den [resurstillgänglighet](container-instances-region-availability.md#availability---general) för den region som du distribuerar i för de övre gränserna för processorkärnor och minne som är tillgängliga per behållargruppen. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Vilken underliggande infrastruktur ACI kör på

Azure Container Instances syftar till att vara en serverlös behållare på begäran-tjänst, så att vi vill att du ska vara fokuserar på att utveckla dina behållare och inte oroa dig om infrastrukturen! För de som är nyfiken eller wanting göra jämförelser på prestanda, körs ACI på uppsättningar av virtuella datorer i Azure över olika SKU: er, främst från F och D-serien. Vi förväntar oss att ändras i framtiden när vi fortsätter att utveckla och optimera tjänsten. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Jag vill distribuera tusen kärnor på ACI - kan jag få min kvoten?
 
Ja (ibland). Se den [kvoter och begränsningar](container-instances-quotas.md) om aktuella kvoter och vilka begränsningar kan utökas av begäran.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Kan jag distribuera med mer än 4 kärnor och 16 GB RAM-minne?

Inte ännu. Det här är för närvarande är den maximala storleken för en behållargrupp. Kontakta Azure-supporten med särskilda krav eller begäranden. 

### <a name="when-will-aci-be-in-a-specific-region"></a>När kommer ACI finnas i en viss region?

Aktuell regiontillgänglighet publiceras [här](container-instances-region-availability.md#availability---general), och aktuell information är tillgänglig via den [funktionerna](/rest/api/container-instances/listcapabilities) API. Om du har ett krav för en viss region kan du kontakta Azure-supporten.

## <a name="features-and-scenarios"></a>Funktioner och scenarier

### <a name="how-do-i-scale-a-container-group"></a>Hur skalar jag en behållargrupp?

För närvarande är skalning inte tillgänglig för behållare eller behållargrupper. Om du vill köra flera instanser kan du använda vårt API för att automatisera och skapa fler begäranden för skapande av behållare grupp till tjänsten. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Vilka funktioner är tillgängliga för instanser som körs i ett anpassat virtuellt nätverk?

Du kan distribuera behållargrupper i Azure-nätverk väljer och delegera privata IP-adresser till behållargrupper att dirigera trafik inom det virtuella nätverket i Azure-resurser. Distribution av en behållargrupp till ett virtuellt nätverk är för närvarande preliminärt och vissa aspekter av den här funktionen kan ändras innan den allmänt tillgängliga (GA). Se [begränsningar i förhandsversionen](container-instances-vnet.md#preview-limitations) uppdaterad information.

## <a name="pricing"></a>Prissättning

### <a name="when-does-the-meter-start-running"></a>När mätaren startar körs?

Varaktighet för behållargrupp beräknas från den tidpunkt då vi börjar att hämta din första behållares avbildning (för en ny distribution) eller din behållargrupp har startats om (om det redan har distribuerats) tills behållargruppen stoppas. Se information på [Container Instances priser](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Förhindrar jag att debiteras när min behållare stoppas?

Taxor stoppa körningen när din hela behållargrupp har stoppats. Så länge som kör en behållare i din behållargrupp kan lagra vi resurser om du vill starta behållarna igen. 

## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-instances-overview.md) om Azure Container Instances.
* [Felsök vanliga problem med](container-instances-troubleshooting.md) i Azure Container Instances.