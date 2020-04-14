---
title: Vanliga frågor och svar
description: Svar på vanliga frågor som rör azure container instances-tjänsten
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261626"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Vanliga frågor och svar om Azure Container-instanser

Den här artikeln tar upp vanliga frågor om Azure Container Instances.

## <a name="deployment"></a>Distribution

### <a name="how-large-can-my-container-image-be"></a>Hur stor kan min behållaravbildning vara?

Den maximala storleken för en distributionsbar behållaravbildning på Azure Container Instances är 15 GB. Du kanske kan distribuera större avbildningar beroende på den exakta tillgängligheten för tillfället du distribuerar, men detta är inte garanterat.

Storleken på behållaravbildningen påverkar hur lång tid det tar att distribuera, så i allmänhet vill du hålla behållaravbildningarna så små som möjligt.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hur kan jag påskynda distributionen av min behållare?

Eftersom en av de viktigaste faktorerna för distributionstider är avbildningsstorleken, leta efter sätt att minska storleken. Ta bort lager som du inte behöver eller minska storleken på lagren i bilden (genom att välja en ljusare bas-OS-bild). Om du till exempel kör Linux-behållare kan du använda Alpine som basavbildning i stället för en fullständig Ubuntu-server. På samma sätt, för Windows-behållare, använd en Nano Server basavbildning om möjligt. 

Du bör också kontrollera listan över förcacherade avbildningar i Azure Container Images, som är tillgängligt via [API:et för lista cachelagrade avbildningar.](/rest/api/container-instances/listcachedimages) Du kanske kan växla ut ett bildlager för en av de förcchenda avbildningarna. 

Mer [detaljerad vägledning](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) om hur du minskar starttiden för behållaren.

### <a name="what-windows-base-os-images-are-supported"></a>Vilka Windows-basoperativa OS-avbildningar stöds?

#### <a name="windows-server-2016-base-images"></a>Grundavbildningar i Windows Server 2016

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `10.0.14393.x`: ,`sac2016`
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016`Core : ,`10.0.14393.x`

> [!NOTE]
> Windows-bilder baserade på halvårskanalutgåvan 1709 eller 1803 stöds inte.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 och klientbasbilder (förhandsgranskning)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : , eller tidigare
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`Core `1809` `10.0.17763.914` : , eller tidigare
* [Windows](https://hub.docker.com/_/microsoft-windows) `1809`: `10.0.17763.914` , eller tidigare

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Vilket .NET- eller .NET Core-bildlager ska jag använda i behållaren? 

Använd den minsta bilden som uppfyller dina krav. För Linux kan du använda en *runtime-alpine* .NET Core-avbildning, som har stötts sedan .NET Core 2.1 släpptes. Om du använder hela .NET Framework för Windows måste du använda en Windows Server Core-avbildning (runtime-only image, till exempel *4.7.2-windowsservercore-ltsc2016*). Körningsavbildningar är mindre men stöder inte arbetsbelastningar som kräver .NET SDK.

## <a name="availability-and-quotas"></a>Tillgänglighet och kvoter

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hur många kärnor och minne ska jag allokera för mina behållare eller behållargruppen?

Detta beror verkligen på din arbetsbelastning. Starta små och testa prestanda för att se hur dina behållare gör. [Övervaka CPU- och minnesresursanvändning](container-instances-monitor.md)och lägg sedan till kärnor eller minne baserat på den typ av processer som du distribuerar i behållaren. 

Kontrollera även [resurstillgängligheten](container-instances-region-availability.md#availability---general) för den region som du distribuerar i för de övre gränserna på CPU-kärnor och tillgängligt minne per behållargrupp. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Vilken underliggande infrastruktur körs ACI på?

Azure Container Instances syftar till att vara en serverlös behållare-on-demand-tjänst, så vi vill att du ska fokusera på att utveckla dina behållare och inte oroa dig för infrastrukturen! För dem som är nyfikna eller vill göra jämförelser på prestanda körs ACI på uppsättningar av virtuella Azure-datorer i olika SKU:er, främst från F- och D-serien. Vi förväntar oss att detta kommer att förändras i framtiden när vi fortsätter att utveckla och optimera tjänsten. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Jag vill distribuera tusentals kärnor på ACI - kan jag få min kvot ökande?
 
Ja (ibland). Se artikeln [om kvoter och begränsningar](container-instances-quotas.md) för de nuvarande kvoterna och vilka gränser som kan höjas genom begäran.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Kan jag distribuera med mer än 4 kärnor och 16 GB RAM?

Inte ännu. För närvarande är dessa maximum för en behållargrupp. Kontakta Azure Support med specifika krav eller begäranden. 

### <a name="when-will-aci-be-in-a-specific-region"></a>När kommer ACI att betas i en viss region?

Aktuell regiontillgänglighet publiceras [här](container-instances-region-availability.md#availability---general). Om du har ett krav för en viss region kontaktar du Azure Support.

## <a name="features-and-scenarios"></a>Funktioner och scenarier

### <a name="how-do-i-scale-a-container-group"></a>Hur skalar jag en behållargrupp?

För närvarande är skalning inte tillgängligt för behållare eller behållargrupper. Om du behöver köra fler instanser kan du använda vårt API för att automatisera och skapa fler begäranden om att skapa behållargrupper till tjänsten. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Vilka funktioner är tillgängliga för instanser som körs i ett anpassat virtuella nätverk?

Du kan [distribuera behållargrupper i ett Azure-virtuellt nätverk](container-instances-vnet.md) som du väljer och delegera privata IP-adresser till behållargrupperna för att dirigera trafik inom det virtuella nätverket över dina Azure-resurser. Distribution av en behållargrupp till ett virtuellt nätverk är för närvarande tillgänglig för produktionsarbetsbelastningar i en delmängd av Azure-regioner.

## <a name="pricing"></a>Prissättning

### <a name="when-does-the-meter-start-running"></a>När börjar mätaren gå?

Varaktigheten för behållargruppen beräknas från den tid som vi börjar hämta den första behållarens avbildning (för en ny distribution) eller om behållargruppen startas om (om den redan har distribuerats) tills behållargruppen stoppas. Se information om [priser för behållarinstanser](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Slutar jag debiteras när mina behållare stoppas?

Mätare slutar köras när hela behållargruppen har stoppats. Så länge en behållare i din behållargrupp körs håller vi resurserna om du vill starta behållarna igen. 

## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-instances-overview.md) om Azure Container Instances.
* [Felsöka vanliga problem](container-instances-troubleshooting.md) i Azure Container Instances.