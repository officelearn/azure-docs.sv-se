---
title: IP-adress typer i Azure (klassisk)
titlesuffix: Azure Virtual Network
description: Lär dig mer om offentliga och privata IP-adresser (klassisk) i Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 7b197e1acf696c2ae6e919ee2eddacfb82ac3802
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646786"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-adress typer och autentiseringsmetoder (klassisk) i Azure
Du kan tilldela IP-adresser till Azure-resurser för att kommunicera med andra Azure-resurser, det lokala nätverket och Internet. Det finns två typer av IP-adresser som du kan använda i Azure: Public och private.

Offentliga IP-adresser används för kommunikation med Internet, inklusive offentliga Azure-tjänster.

Privata IP-adresser används för kommunikation inom ett virtuellt Azure-nätverk (VNet), en moln tjänst och ditt lokala nätverk när du använder en VPN-gateway eller ExpressRoute-krets för att utöka ditt nätverk till Azure.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager. Läs mer om IP-adresser i Resource Manager genom att läsa artikeln [IP-adresser](virtual-network-ip-addresses-overview-arm.md) .

## <a name="public-ip-addresses"></a>Offentliga IP-adresser
Offentliga IP-adresser gör att Azure-resurser kan kommunicera med Internet och Azures offentliga tjänster som [Azure cache för Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-databaser](../sql-database/sql-database-technical-overview.md)och [Azure Storage](../storage/common/storage-introduction.md).

En offentlig IP-adress är associerad med följande resurs typer:

* Cloud services
* IaaS Virtual Machines (VM)
* PaaS roll instanser
* VPN-gatewayer
* Programgateways

### <a name="allocation-method"></a>Allokeringsmetod
När en offentlig IP-adress måste tilldelas en Azure-resurs, tilldelas den *dynamiskt* från en pool med tillgänglig offentlig IP-adress på den plats där resursen skapas. Den här IP-adressen släpps när resursen stoppas. Med moln tjänsten inträffar detta när alla roll instanser stoppas, vilket kan undvikas genom att använda en *statisk* (reserverad) IP-adress (se [Cloud Services](#cloud-services)).

> [!NOTE]
> Listan över IP-adressintervall från vilka offentliga IP-adresser tilldelas till Azure-resurser publiceras i [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Matchning av DNS-värdnamn
När du skapar en moln tjänst eller en virtuell IaaS-dator måste du ange ett DNS-namn för moln tjänsten som är unikt för alla resurser i Azure. Detta skapar en mappning i de Azure-hanterade DNS-servrarna för *dnsname*. cloudapp.net till resursens offentliga IP-adress. När du till exempel skapar en moln tjänst med DNS-namnet **contoso**i moln tjänsten kommer det fullständigt kvalificerade domän namnet (FQDN) **contoso.cloudapp.net** att matchas mot en offentlig IP-adress (VIP) för moln tjänsten. Du kan använda detta fullständiga domännamn för att skapa en anpassad CNAME-domänpost som pekar på den offentliga IP-adressen i Azure.

### <a name="cloud-services"></a>Cloud services
En moln tjänst har alltid en offentlig IP-adress som kallas virtuell IP-adress (VIP). Du kan skapa slut punkter i en moln tjänst för att associera olika portar i VIP till interna portar på virtuella datorer och roll instanser i moln tjänsten. 

En moln tjänst kan innehålla flera virtuella IaaS-datorer, eller PaaS roll instanser, som alla exponeras via samma moln tjänst VIP. Du kan också tilldela flera VIP-adresser [till en moln tjänst](../load-balancer/load-balancer-multivip.md), vilket möjliggör flera VIP-scenarier som flera klient miljöer med SSL-baserade webbplatser.

Du kan se till att den offentliga IP-adressen för en moln tjänst är oförändrad, även när alla roll instanser stoppas, genom att använda en *statisk* offentlig IP-adress, som kallas [reserverad IP](virtual-networks-reserved-public-ip.md). Du kan skapa en statisk (reserverad) IP-resurs på en angiven plats och tilldela den till valfri moln tjänst på den platsen. Du kan inte ange den faktiska IP-adressen för den reserverade IP-adressen. den allokeras från poolen med tillgängliga IP-adresser på den plats där den skapas. Den här IP-adressen släpps inte förrän du tar bort den explicit.

Statiska (reserverade) offentliga IP-adresser används ofta i scenarier där en moln tjänst:

* kräver att brand Väggs regler konfigureras av slutanvändare.
* beroende av extern DNS-namnmatchning och en dynamisk IP-adress skulle kräva uppdatering av poster.
* använder externa webb tjänster som använder IP-baserad säkerhets modell.
* använder SSL-certifikat som är länkade till en IP-adress.

> [!NOTE]
> När du skapar en klassisk virtuell dator skapas en behållare för *moln tjänster* av Azure, som har en virtuell IP-adress (VIP). När skapandet görs via portalen konfigureras en standard-RDP-eller SSH- *slutpunkt* av portalen, så att du kan ansluta till den virtuella datorn via moln tjänsten VIP. Den här moln tjänstens VIP kan reserveras, vilket effektivt ger en reserverad IP-adress för att ansluta till den virtuella datorn. Du kan öppna ytterligare portar genom att konfigurera fler slut punkter.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VM och PaaS roll instanser
Du kan tilldela en offentlig IP-adress direkt till en IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -eller PaaS-roll instans i en moln tjänst. Detta kallas för en offentlig IP-adress på instans nivå ([ILPIP](virtual-networks-instance-level-public-ip.md)). Den här offentliga IP-adressen kan bara vara dynamisk.

> [!NOTE]
> Detta skiljer sig från VIP för moln tjänsten, som är en behållare för virtuella IaaS-datorer eller PaaS-roll instanser, eftersom en moln tjänst kan innehålla flera IaaS-VM: ar, eller PaaS roll instanser, som alla exponeras genom samma virtuella-moln tjänst.
> 
> 

### <a name="vpn-gateways"></a>VPN-gatewayer
En [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) kan användas för att ansluta ett Azure VNet till andra Azure-virtuella nätverk eller lokala nätverk. En VPN-gateway tilldelas en offentlig IP-adress *dynamiskt*, vilket möjliggör kommunikation med fjärrnätverket.

### <a name="application-gateways"></a>Programgateways
En Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) kan användas för Layer7 belastnings utjämning för att dirigera nätverks trafik baserat på http. Application Gateway tilldelas en offentlig IP-adress *dynamiskt*, som fungerar som belastningsutjämnad VIP.

### <a name="at-a-glance"></a>En snabbtitt
Tabellen nedan visar varje resurs typ med möjliga fördelnings metoder (dynamisk/statisk) och möjlighet att tilldela flera offentliga IP-adresser.

| Resurs | Dynamisk | Statisk | Flera IP-adresser |
| --- | --- | --- | --- |
| Molntjänst |Ja |Ja |Ja |
| IaaS VM eller PaaS roll instans |Ja |Inga |Inga |
| VPN gateway |Ja |Inga |Inga |
| Programgateway |Ja |Inga |Inga |

## <a name="private-ip-addresses"></a>Privata IP-adresser
Privata IP-adresser gör att Azure-resurser kan kommunicera med andra resurser i en moln tjänst eller ett [virtuellt nätverk](virtual-networks-overview.md)(VNet) eller till ett lokalt nätverk (via en VPN-gateway eller ExpressRoute-krets) utan att använda en IP-adress som kan användas på Internet.

I den klassiska Azure-distributions modellen kan en privat IP-adress tilldelas till följande Azure-resurser:

* IaaS VM och PaaS roll instanser
* Intern lastbalanserare
* Programgateway

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VM och PaaS roll instanser
Virtuella datorer (VM) som skapats med den klassiska distributions modellen placeras alltid i en moln tjänst, som liknar PaaS roll instanser. Beteendet för privata IP-adresser liknar detta för dessa resurser.

Det är viktigt att Observera att en moln tjänst kan distribueras på två sätt:

* Som en *fristående* moln tjänst där den inte är inom ett virtuellt nätverk.
* Som en del av ett virtuellt nätverk.

#### <a name="allocation-method"></a>Allokeringsmetod
I händelse av en *fristående* moln tjänst får resurserna en privat IP-adress som tilldelas *dynamiskt* från Azure DATAcenters privata IP-adressintervall. Den kan endast användas för kommunikation med andra virtuella datorer i samma moln tjänst. Den här IP-adressen kan ändras när resursen stoppas och startas.

Om en moln tjänst har distribuerats i ett virtuellt nätverk hämtar resurser privata IP-adresser som tilldelas från adress intervallet för de associerade under näten (som anges i dess nätverks konfiguration). Den här privata IP-adressen (ES) kan användas för kommunikation mellan alla virtuella datorer i VNet.

I händelse av moln tjänster i ett virtuellt nätverk tilldelas dessutom en privat IP-adress *dynamiskt* (med DHCP) som standard. Den kan ändras när resursen stoppas och startas. För att se till att IP-adressen förblir densamma måste du ställa in fördelnings metoden på *statisk*och ange en giltig IP-adress inom motsvarande adress intervall.

Statiska privata IP-adresser används ofta för:

* Virtuella datorer som fungerar som domänkontrollanter eller DNS-servrar.
* Virtuella datorer som kräver brand Väggs regler med IP-adresser.
* Virtuella datorer som kör tjänster som används av andra appar via en IP-adress.

#### <a name="internal-dns-hostname-resolution"></a>Intern DNS-värdnamn matchning
Alla roll instanser för virtuella Azure-datorer och PaaS konfigureras med [Azure-hanterade DNS-servrar](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard, om du inte uttryckligen konfigurerar anpassade DNS-servrar. Dessa DNS-servrar tillhandahåller intern namn matchning för virtuella datorer och roll instanser som finns i samma VNet-eller moln tjänst.

När du skapar en virtuell dator läggs en mappning till för värdnamnet till dess privata IP-adress för de Azure-hanterade DNS-servrarna. Med en virtuell dator med flera nätverkskort mappas värd namnet till den privata IP-adressen för det primära NÄTVERKSKORTet. Mappnings informationen är dock begränsad till resurser inom samma moln tjänst eller VNet.

Om du använder en *fristående* moln tjänst kan du bara matcha värdnamn för alla virtuella datorer/roll instanser i samma moln tjänst. Om det är en moln tjänst i ett VNet kan du matcha värdnamn för alla virtuella datorer/roll instanser i VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interna lastbalanserare (ILB) och programgateways
Du kan tilldela en privat IP-adress till konfigurationen på **klientsidan** för en [intern lastbalanserare i Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) eller till en [programgateway i Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Den här privata IP-adressen fungerar som en intern slutpunkt som bara kan nås av resurserna i dess virtuella nätverk (VNet) och fjärrnätverken som är anslutna till det virtuella nätverket. Du kan tilldela antingen en dynamisk eller privat IP-adress till konfigurationen på klientsidan. Du kan också tilldela flera privata IP-adresser för att aktivera flera VIP-scenarier.

### <a name="at-a-glance"></a>En snabbtitt
Tabellen nedan visar varje resurs typ med möjliga fördelnings metoder (dynamisk/statisk) och möjlighet att tilldela flera privata IP-adresser.

| Resurs | Dynamisk | Statisk | Flera IP-adresser |
| --- | --- | --- | --- |
| Virtuell dator (i en *fristående* moln tjänst eller ett virtuellt nätverk) |Ja |Ja |Ja |
| PaaS roll instans (i en *fristående* moln tjänst eller ett virtuellt nätverk) |Ja |Inga |Inga |
| Klient delen för intern belastningsutjämnare |Ja |Ja |Ja |
| Klient delen för Application Gateway |Ja |Ja |Ja |

## <a name="limits"></a>Begränsningar
I tabellen nedan visas de begränsningar som gäller för IP-adresser i Azure per prenumeration. Du kan [kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standardgränserna upp till de maximala gränserna utifrån dina affärsbehov.

|  | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser (dynamiska) |5 |kontakta supporten |
| Reserverade offentliga IP-adresser |20 |kontakta supporten |
| Offentlig VIP per distribution (moln tjänst) |5 |kontakta supporten |
| Privat VIP (ILB) per distribution (moln tjänst) |1 |1 |

Se till att läsa den fullständiga uppsättningen [begränsningar för nätverk](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) i Azure.

## <a name="pricing"></a>Prissättning
I de flesta fall är offentliga IP-adresser kostnads fria. Det finns en nominell avgift för att använda ytterligare och/eller statiska offentliga IP-adresser. Se till att du förstår [pris strukturen för offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Skillnader mellan Resource Manager och klassiska distributioner
Nedan visas en jämförelse av funktioner för IP-adresser i Resource Manager och den klassiska distributions modellen.

|  | Resurs | Klassisk | Resurshanterare |
| --- | --- | --- | --- |
| **Offentlig IP-adress** |***VM*** |Kallas en ILPIP (endast dynamisk) |Kallas för offentlig IP (dynamisk eller statisk) |
|  ||Tilldelad en virtuell IaaS-dator eller en PaaS-roll instans |Kopplad till den virtuella datorns nätverkskort |
|  |***Belastningsutjämnare mot Internet*** |Kallas VIP (dynamisk) eller Reserverad IP (statisk) |Kallas för offentlig IP (dynamisk eller statisk) |
|  ||Tilldelad till en moln tjänst |Associeras med Load Balancer-konfigurationen på klient Sidan |
|  | | | |
| **Privat IP-adress** |***VM*** |Kallas DIP |Kallas privat IP-adress |
|  ||Tilldelad en virtuell IaaS-dator eller en PaaS-roll instans |Tilldelad till den virtuella datorns nätverkskort |
|  |***Intern belastningsutjämnare (ILB)*** |Tilldelad ILB (dynamisk eller statisk) |Tilldelad till ILB konfiguration av klient delen (dynamisk eller statisk) |

## <a name="next-steps"></a>Nästa steg
* [Distribuera en virtuell dator med en statisk privat IP-adress](virtual-networks-static-private-ip-classic-pportal.md) med hjälp av Azure Portal.

