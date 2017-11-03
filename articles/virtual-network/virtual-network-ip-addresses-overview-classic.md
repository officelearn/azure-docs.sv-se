---
title: Typer av IP-adress i Azure (klassisk) | Microsoft Docs
description: "Läs mer om offentliga och privata IP-adresser (klassisk) i Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: aa99d3ebd181a76d582670c5219f29858c462d30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-adresstyper och allokeringsmetoder (klassiskt) i Azure
Du kan tilldela IP-adresser till Azure-resurser för att kommunicera med andra Azure-resurser, det lokala nätverket och Internet. Det finns två typer av IP-adresser som du kan använda i Azure: offentliga och privata.

Offentliga IP-adresser används för kommunikation med Internet, inklusive Azure offentliga-tjänster.

Privata IP-adresser används för kommunikation inom Azure-nätverk (VNet), en tjänst i molnet och lokala nätverk när du använder en VPN-gateway eller ExpressRoute-krets för att utöka ditt nätverk till Azure.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager. Lär dig mer om IP-adresser i Resource Manager genom att läsa den [IP-adresser](virtual-network-ip-addresses-overview-arm.md) artikel.

## <a name="public-ip-addresses"></a>Offentliga IP-adresser
Offentliga IP-adresser gör att Azure-resurser kan kommunicera med Internet och offentliga Azure-tjänster som [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL Database](../sql-database/sql-database-technical-overview.md) och [Azure Storage](../storage/common/storage-introduction.md).

En offentlig IP-adress är kopplat till följande resurstyper:

* Molntjänster
* IaaS-virtuella maskiner (VMs)
* PaaS-rollinstanser
* VPN-gateways
* Programgateways

### <a name="allocation-method"></a>Allokeringsmetod
När en offentlig IP-adress måste tilldelas till en Azure-resurs, är det *dynamiskt* allokeras från en pool med tillgängliga offentliga IP-adress inom platsen för resursen har skapats. Den här IP-adressen frisläpps när resursen har stoppats. Om detta händer när alla rollinstanser har stoppats av en tjänst i molnet, vilket kan undvikas genom att använda en *Statiska* (reserverad) IP-adress (se [molntjänster](#Cloud-services)).

> [!NOTE]
> Listan över IP-adressintervall som offentliga IP-adresser som är allokerade till Azure-resurser som har publicerats på [Azure Datacenter-IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Matchning av DNS-värdnamn
Du måste ange ett moln tjänsten DNS-namn som är unikt över alla resurser i Azure när du skapar en molnbaserad tjänst eller ett IaaS-VM. Detta skapar en mappning i Azure-hanterade DNS-servrar för *dnsname*. cloudapp.net offentliga IP-adressen för resursen. Till exempel när du skapar en tjänst i molnet med cloud service DNS-namnet **contoso**, det fullständigt kvalificerade domännamnet (FQDN) **contoso.cloudapp.net** matchar med en offentlig IP-adress (VIP) för Molntjänsten. Du kan använda detta fullständiga domännamn för att skapa en anpassad CNAME-domänpost som pekar på den offentliga IP-adressen i Azure.

### <a name="cloud-services"></a>Molntjänster
En molnbaserad tjänst har alltid en offentlig IP-adress som kallas en virtuell IP-adress (VIP). Du kan skapa slutpunkter i en molntjänst för att associera olika portar i VIP till interna portar på virtuella datorer och rollinstanser i Molntjänsten. 

En molnbaserad tjänst kan innehålla flera IaaS-VM eller PaaS-rollinstanser, alla exponerade via samma cloud service VIP. Du kan också tilldela [flera VIP: er till en molntjänst](../load-balancer/load-balancer-multivip.md), vilket gör att multi-VIP-scenarier som klientmiljön med SSL-baserad webbplatser.

Du kan kontrollera offentliga IP-adressen för en tjänst i molnet förblir detsamma även när alla rollinstanser har stoppats, med hjälp av en *Statiska* offentlig IP-adress, kallas [reserverade IP: N](virtual-networks-reserved-public-ip.md). Du kan skapa en statisk (reserverad) IP-resurs på en specifik plats och tilldela den till en tjänst i molnet på den platsen. Du kan inte ange den IP-adressen för den reserverade IP: N, allokeras från pool med tillgängliga IP-adresser på den plats som den har skapats. IP-adressen frisläpps inte förrän du uttryckligen har tagit bort den.

Statisk (reserverad) offentliga IP-adresser används vanligen i scenarier där en tjänst i molnet:

* kräver brandväggsregler konfigureras av slutanvändare.
* beror på externa DNS-namnmatchning och kräver en dynamisk IP-adress uppdaterar A-poster.
* förbrukar externa webbtjänster som använder IP-baserade säkerhetsmodell.
* använder SSL-certifikat som är kopplad till en IP-adress.

> [!NOTE]
> När du skapar en klassisk virtuell dator, en behållare *Molntjänsten* har skapats med Azure som har en virtuell IP-adress (VIP). När du är klar skapas via portalen standard RDP eller SSH *endpoint* konfigureras av portalen så att du kan ansluta till den virtuella datorn via cloud service VIP. Det här molnet tjänsten VIP kan reserveras, vilket ger en reserverad IP-adress för att ansluta till den virtuella datorn. Du kan öppna ytterligare portar genom att konfigurera fler slutpunkter.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS-VM och PaaS-rollinstanser
Du kan tilldela en offentlig IP-adress direkt till en IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller PaaS rollinstans i en molntjänst. Detta kallas en offentlig IP-adress för instansnivå ([går](virtual-networks-instance-level-public-ip.md)). Den här offentliga IP-adressen kan bara vara dynamisk.

> [!NOTE]
> Detta skiljer sig från VIP till Molntjänsten, som är en behållare för IaaS-VM eller PaaS-rollinstanser, eftersom en molntjänst kan innehålla flera IaaS-VM eller PaaS-rollinstanser alla exponeras via samma cloud service VIP.
> 
> 

### <a name="vpn-gateways"></a>VPN-gateways
En [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) kan användas för att ansluta ett virtuellt Azure-nätverk till andra virtuella Azure-nätverk eller lokala nätverk. En VPN-gateway har tilldelats en offentlig IP-adress *dynamiskt*, som aktiverar kommunikation med fjärrnätverket.

### <a name="application-gateways"></a>Programgateways
En Azure [Programgateway](../application-gateway/application-gateway-introduction.md) kan användas för Layer7 belastningsutjämning att dirigera nätverkstrafik baserat på HTTP. Programgateway har tilldelats en offentlig IP-adress *dynamiskt*, som fungerar som det belastningsutjämnade VIP.

### <a name="at-a-glance"></a>En snabbtitt
Tabellen nedan visar varje resurstyp med möjliga allokeringsmetoder (dynamisk/statisk) och möjligheten att tilldela flera offentliga IP-adresser.

| Resurs | Dynamisk | Statisk | Flera IP-adresser |
| --- | --- | --- | --- |
| Molntjänsten |Ja |Ja |Ja |
| IaaS-VM eller PaaS rollinstans |Ja |Nej |Nej |
| VPN-gateway |Ja |Nej |Nej |
| Programgateway |Ja |Nej |Nej |

## <a name="private-ip-addresses"></a>Privata IP-adresser
Privata IP-adresser låter Azure-resurser kommunicera med andra resurser i en molnbaserad tjänst eller en [virtuellt nätverk](virtual-networks-overview.md)(VNet), eller till lokalt nätverk (via en VPN-gateway eller ExpressRoute-krets), utan att använda en Internet-nåbar IP-adress.

I Azure klassiska distributionsmodellen kan en privat IP-adress tilldelas följande Azure-resurser:

* IaaS-VM och PaaS-rollinstanser
* Intern belastningsutjämnare
* Programgateway

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS-VM och PaaS-rollinstanser
Virtuella datorer (VM) som skapats med den klassiska distributionsmodellen placeras alltid i en molntjänst som liknar PaaS-rollinstanser. Beteendet för privata IP-adresser är därför liknande för dessa resurser.

Det är viktigt att Observera att en tjänst i molnet kan distribueras på två sätt:

* Som en *fristående* Molntjänsten, om det inte är inom ett virtuellt nätverk.
* Som en del av ett virtuellt nätverk.

#### <a name="allocation-method"></a>Allokeringsmetod
I händelse av en *fristående* Molntjänsten, resurser get en privat IP-adress tilldelas *dynamiskt* från Azure-datacenter privata IP-adressintervall. Det kan användas för kommunikation med andra virtuella datorer i samma molntjänst. Denna IP-adress kan ändra när resursen stoppas och startas.

Vid en tjänst i molnet distribueras inom ett virtuellt nätverk, hämta resurser privata IP-adresser som allokerats från adressintervall associerade adressundernät (som anges i nätverkskonfigurationen). Det här privata IP-adresser kan användas för kommunikation mellan alla virtuella datorer inom VNet.

Dessutom vid molntjänster inom ett VNet, en privat IP-adress tilldelas *dynamiskt* (med DHCP) som standard. Den kan ändras när resursen stoppas och startas. För att säkerställa IP-adress är densamma, måste du ange allokeringsmetoden *Statiska*, och ange en giltig IP-adress inom det motsvarande adressintervallet.

Statiska privata IP-adresser används ofta för:

* Virtuella datorer som fungerar som domänkontrollanter eller DNS-servrar.
* Virtuella datorer som kräver brandväggsregler med hjälp av IP-adresser.
* Virtuella datorer som kör tjänster som används av andra appar via en IP-adress.

#### <a name="internal-dns-hostname-resolution"></a>Interna DNS-värdnamn
Alla virtuella datorer i Azure och PaaS-rollinstanser har konfigurerats med [Azure-hanterade DNS-servrar](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard om du inte uttryckligen anger anpassade DNS-servrar. Dessa DNS-servrar kan du ange intern namnmatchning för virtuella datorer och rollinstanser som finns på samma virtuella nätverk eller molnbaserade tjänst.

När du skapar en virtuell dator läggs en mappning till för värdnamnet till dess privata IP-adress för de Azure-hanterade DNS-servrarna. Vid en VM med flera nätverkskort är värdnamnet mappad till den privata IP-adressen för det primära nätverkskortet. Den här mappningsinformation är dock begränsad till resurser i samma molntjänst eller virtuella nätverk.

I händelse av en *fristående* Molntjänsten, du kommer att kunna matcha värdnamn för alla virtuella datorer/rollinstanser inom samma Molntjänsten endast. Vid en molnbaserad tjänst inom ett VNet, kommer du att kunna matcha värdnamn för alla instanser för virtuella datorer/roll inom VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interna belastningsutjämnare (ILB) och programgateways
Du kan tilldela en privat IP-adress till konfigurationen på **klientsidan** för en [intern belastningsutjämnare i Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) eller till en [programgateway i Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Den här privata IP-adressen fungerar som en intern slutpunkt som bara kan nås av resurserna i dess virtuella nätverk (VNet) och fjärrnätverken som är anslutna till det virtuella nätverket. Du kan tilldela antingen en dynamisk eller privat IP-adress till konfigurationen på klientsidan. Du kan också tilldela flera privata IP-adresser för att aktivera scenarier med multi-vip.

### <a name="at-a-glance"></a>En snabbtitt
Tabellen nedan visar varje resurstyp med möjliga allokeringsmetoder (dynamisk/statisk) och möjligheten att tilldela flera privata IP-adresser.

| Resurs | Dynamisk | Statisk | Flera IP-adresser |
| --- | --- | --- | --- |
| Virtuell dator (i en *fristående* Molntjänsten) |Ja |Ja |Ja |
| PaaS rollinstansen (i en *fristående* Molntjänsten) |Ja |Nej |Ja |
| Virtuell dator eller PaaS rollinstansen (i ett virtuellt nätverk) |Ja |Ja |Ja |
| Interna belastningsutjämnare klientdel |Ja |Ja |Ja |
| Programmet gateway-klientdel |Ja |Ja |Ja |

## <a name="limits"></a>Begränsningar
I tabellen nedan visas de begränsningar för IP-adresser i Azure per prenumeration. Du kan [kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standardgränserna upp till de maximala gränserna utifrån dina affärsbehov.

|  | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser (dynamiska) |5 |kontakta supporten |
| Reserverade offentliga IP-adresser |20 |kontakta supporten |
| Offentliga VIP per distribution (Molntjänsten) |5 |kontakta supporten |
| Privata VIP (ILB) per distribution (Molntjänsten) |1 |1 |

Kontrollera att du läser den fullständiga uppsättningen [begränsar för nätverk](../azure-subscription-service-limits.md#networking-limits) i Azure.

## <a name="pricing"></a>Prissättning
I de flesta fall är offentliga IP-adresser kostnadsfri. Det kostar en nominell ytterligare och/eller statiska offentliga IP-adresser. Kontrollera att du förstår de [priser strukturen för offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Skillnader mellan Resource Manager och klassiska distributioner
Nedan visas en jämförelse av IP-adressering funktioner i Resource Manager och den klassiska distributionsmodellen.

|  | Resurs | Klassisk | Resource Manager |
| --- | --- | --- | --- |
| **Offentlig IP-adress** |***VM*** |Kallas en går (dynamisk) |Som en offentlig IP-adress (dynamisk eller statisk) |
|  ||Tilldelad till en IaaS-VM eller en PaaS-rollinstans |Som är kopplade till den Virtuella datorns nätverkskort | |
|  |***Internet belastningsutjämnare*** |Kallas VIP (dynamisk) eller reserverade IP: N (statisk) |Som en offentlig IP-adress (dynamisk eller statisk) | |
|  ||Tilldelad till en tjänst i molnet |Som är kopplat till belastningsutjämnarens klientdelen config | |
|  | | | |
| **Privata IP-adress** |***VM*** |Kallas en DIP |Kallas en privat IP-adress |
|  ||Tilldelad till en IaaS-VM eller en PaaS-rollinstans |Tilldelade till den Virtuella datorns nätverkskort | |
|  |***Intern belastningsutjämnare (ILB)*** |Tilldelats ILB (dynamisk eller statisk) |Tilldelats av ILB klientdelen configuration (dynamisk eller statisk) | |

## <a name="next-steps"></a>Nästa steg
* [Distribuera en virtuell dator med en statisk privat IP-adress](virtual-networks-static-private-ip-classic-pportal.md) med Azure-portalen.

