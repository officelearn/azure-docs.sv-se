---
title: Vanliga frågor och svar
description: Svar på vanliga frågor som rör Azure Container Instances tjänsten
author: dkkapur
ms.topic: article
ms.date: 01/07/2020
ms.openlocfilehash: 4a3fb4c1818d86f7fe2913790fd9e573c630cbfd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365042"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Vanliga frågor och svar om Azure Container Instances

Den här artikeln handlar om vanliga frågor om Azure Container Instances.

## <a name="deployment"></a>Distribution

### <a name="how-large-can-my-container-image-be"></a>Hur stor kan min behållar avbildning vara?

Den maximala storleken för en distributions bara behållar avbildning på Azure Container Instances är 15 GB. Du kanske kan distribuera större avbildningar beroende på den exakta tillgängligheten när du distribuerar, men detta är inte garanterat.

Storleken på behållar avbildningen påverkar hur lång tid det tar att distribuera, så normalt ska du behålla behållar avbildningarna så små som möjligt.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hur kan jag påskynda distributionen av min behållare?

Eftersom en av de huvudsakliga faktorerna för distributions tider är bild storleken kan du se till att minska storleken på olika sätt. Ta bort lager du inte behöver eller minska storleken på lagren i bilden (genom att välja en ljusare bas operativ system avbildning). Om du till exempel kör Linux-behållare bör du överväga att använda Alpine som bas avbildning i stället för en fullständig Ubuntu-Server. På samma sätt använder du en Nano Server-avbildning om det är möjligt för Windows-behållare. 

Du bör också kontrol lera listan över cachelagrade avbildningar i Azure Container images, som är tillgängliga via API: et för [cachelagrade avbildningar](/rest/api/container-instances/listcachedimages) . Du kanske kan byta ut ett bild lager för en av de förcachelagrade bilderna. 

Mer [detaljerad](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) information om hur du minskar start tiden för behållare.

### <a name="what-windows-base-os-images-are-supported"></a>Vilka Windows Base OS-avbildningar stöds?

#### <a name="windows-server-2016-base-images"></a>Bas avbildningar för Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x``sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016``10.0.14393.x`

> [!NOTE]
> Windows-avbildningar baserade på halvårs kanal version 1709 eller 1803 stöds inte.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 och client Base images (för hands version)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809``10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019``1809``10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809``10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Vilket .NET-eller .NET Core-avbildnings lager ska jag använda i min behållare? 

Använd den minsta avbildning som uppfyller dina krav. För Linux kan du använda en *runtime-Alpine* .net Core-avbildning, som har stöd för sedan lanseringen av .net Core 2,1. Om du använder den fullständiga .NET Framework för Windows måste du använda en Windows Server Core-avbildning (endast körnings avbildning, till exempel *4.7.2-windowsservercore-ltsc2016*). Endast körnings avbildningar är mindre, men har inte stöd för arbets belastningar som kräver .NET SDK.

## <a name="availability-and-quotas"></a>Tillgänglighet och kvoter

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hur många kärnor och minne ska jag allokera för mina behållare eller behållar gruppen?

Det beror faktiskt på din arbets belastning. Starta små och test prestanda för att se hur dina behållare gör. [Övervaka användningen av processor-och minnes resurser](container-instances-monitor.md)och Lägg sedan till kärnor eller minne baserat på den typ av processer som du distribuerar i behållaren. 

Se till att även kontrol lera [resurs tillgängligheten](container-instances-region-availability.md#availability---general) för den region som du distribuerar i för de övre gränserna för processor kärnor och tillgängligt minne per behållar grupp. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Vilken underliggande infrastruktur körs ACI på?

Azure Container Instances syftar på att vara en server lös behållare – på begäran, så vi vill att du ska fokusera på att utveckla dina behållare och inte bekymra dig om infrastrukturen! För de som är nyfiken på eller vill göra jämförelser av prestanda körs ACI på uppsättningar med virtuella Azure-datorer av olika SKU: er, främst från F och D-serien. Vi förväntar oss att ändra i framtiden när vi fortsätter att utveckla och optimera tjänsten. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Jag vill distribuera tusen kärnor på ACI – kan jag få min kvot utökad?
 
Ja (ibland). Se artikeln [kvoter och begränsningar](container-instances-quotas.md) för aktuella kvoter och vilka gränser som kan ökas på begäran.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Kan jag distribuera med fler än 4 kärnor och 16 GB RAM-minne?

Inte ännu. För närvarande är detta de maximala värdena för en behållar grupp. Kontakta Azure-supporten med särskilda krav eller begär Anden. 

### <a name="when-will-aci-be-in-a-specific-region"></a>När kommer ACI finnas i en speciell region?

Aktuell regions tillgänglighet publiceras [här](container-instances-region-availability.md#availability---general). Kontakta Azure-supporten om du har ett krav för en speciell region.

## <a name="features-and-scenarios"></a>Funktioner och scenarier

### <a name="how-do-i-scale-a-container-group"></a>Vill du Hur gör jag för att skala en behållar grupp?

För närvarande är skalning inte tillgängligt för behållare eller behållar grupper. Om du behöver köra fler instanser använder du vårt API för att automatisera och skapa fler begär Anden för att skapa behållar grupper till tjänsten. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Vilka funktioner är tillgängliga för instanser som körs i ett anpassat VNet?

Du kan [distribuera behållar grupper i ett virtuellt Azure-nätverk](container-instances-vnet.md) och delegera privata IP-adresser till behållar grupper för att dirigera trafik i VNet över dina Azure-resurser. Distribution av en behållar grupp i ett virtuellt nätverk är för närvarande tillgängligt för produktions arbets belastningar i en delmängd av Azure-regioner.

## <a name="pricing"></a>Priser

### <a name="when-does-the-meter-start-running"></a>När körs mätaren?

Varaktigheten för container gruppen beräknas från den tid som vi börjar hämta din första behållares avbildning (för en ny distribution) eller om din behållar grupp startas om (om det redan har distribuerats), tills behållar gruppen stoppas. Se information på [container instances priser](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Upphör jag att debiteras när mina behållare stoppas?

Mätare slutar att köra när hela behållar gruppen har stoppats. Så länge som en behållare i din behållar grupp körs, ska vi lagra resurserna om du vill starta behållarna igen. 

## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-instances-overview.md) om Azure Container instances.
* [Felsök vanliga problem](container-instances-troubleshooting.md) i Azure Container instances.