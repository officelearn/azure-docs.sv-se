---
title: IP-adresstyper i Azure (klassisk)
titlesuffix: Azure Virtual Network
description: Läs mer om offentliga och privata IP-adresser (klassisk) i Azure.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: f96ac14d68d98937cf230b04b45503e21c5e0187
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024577"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-adresstyper och allokeringsmetoder i Azure (klassisk) i Azure
Du kan tilldela IP-adresser till Azure-resurser för att kommunicera med andra Azure-resurser, det lokala nätverket och Internet. Det finns två typer av IP-adresser som du kan använda i Azure: offentliga och privata.

Offentliga IP-adresser som används för kommunikation med Internet, inklusive Azure offentliga tjänster.

Privata IP-adresser används för kommunikation inom ett Azure-nätverk (VNet), en tjänst i molnet och ditt lokala nätverk när du använder en VPN-gateway eller ExpressRoute-krets för att utöka ditt nätverk till Azure.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager. Lär dig mer om IP-adresser i Resource Manager genom att läsa den [IP-adresser](virtual-network-ip-addresses-overview-arm.md) artikeln.

## <a name="public-ip-addresses"></a>Offentliga IP-adresser
Offentliga IP-adresser gör Azure-resurser kan kommunicera med Internet och Azure offentliga tjänster som [Azure Cache för Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-databaser](../sql-database/sql-database-technical-overview.md), och [Azure storage](../storage/common/storage-introduction.md).

En offentlig IP-adress är associerat med följande resurstyper:

* Molntjänster
* IaaS-datorer (VM)
* PaaS-rollinstanser
* VPN-gateways
* Programgateways

### <a name="allocation-method"></a>Allokeringsmetod
När en offentlig IP-adress måste tilldelas till en Azure-resurs, är det *dynamiskt* allokeras från en pool med tillgängliga offentliga IP-adress inom den plats där resursen skapas. Den här IP-adressen frisläpps när resursen har stoppats. Med Molntjänsten, sker detta när alla rollinstanser har stoppats, som kan undvikas genom att använda en *Statiska* (reserverad) IP-adress (se [molntjänster](#Cloud-services)).

> [!NOTE]
> Listan över IP-adressintervall som offentliga IP-adresser allokeras till Azure-resurser finns i avsnittet [Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Matchning av DNS-värdnamn
När du skapar en molnbaserad tjänst eller en IaaS-VM, måste du ange ett moln tjänsten DNS-namn som är unikt i alla resurser i Azure. Detta skapar en mappning i Azure-hanterade DNS-servrar för *dnsname*. cloudapp.net till offentliga IP-adressen för resursen. Till exempel när du skapar en tjänst i molnet med cloud service DNS-namnet **contoso**, det fullständigt kvalificerade domännamnet (FQDN) **contoso.cloudapp.net** matchar med en offentlig IP-adress (VIP) i molnet tjänsten. Du kan använda detta fullständiga domännamn för att skapa en anpassad CNAME-domänpost som pekar på den offentliga IP-adressen i Azure.

### <a name="cloud-services"></a>Molntjänster
En molnbaserad tjänst har alltid en offentlig IP-adress som kallas virtuell IP-adress (VIP). Du kan skapa slutpunkter i en molntjänst för att associera olika portar i VIP till interna portar på virtuella datorer och rollinstanser i Molntjänsten. 

En molnbaserad tjänst kan innehålla flera virtuella IaaS-datorer eller PaaS-rollinstanser, alla exponerade via samma cloud service VIP. Du kan också tilldela [flera virtuella IP-adresser till en molntjänst](../load-balancer/load-balancer-multivip.md), vilket gör att flera Virtuella scenarier som miljö med flera klientorganisationer med SSL-baserade webbplatser.

Du kan kontrollera att den offentliga IP-adressen för en molnbaserad tjänst förblir detsamma även när alla rollinstanser har stoppats, med hjälp av en *Statiska* offentlig IP-adress, kallas [reserverade IP-Adressen](virtual-networks-reserved-public-ip.md). Du kan skapa en statisk (reserverad) IP-resurs på en specifik plats och tilldela den till valfri molntjänst i den platsen. Du kan inte ange själva IP-adressen för den reserverade IP-Adressen, den allokeras från pool med tillgängliga IP-adresser på den plats som den har skapats. Den här IP-adressen frisläpps inte tills du uttryckligen tar bort den.

Statisk (reserverad) offentliga IP-adresser används ofta i scenarier där det är en molnbaserad tjänst:

* kräver brandväggsregler för att ställas in av slutanvändare.
* är beroende av externa DNS-namnmatchning, och en dynamisk IP-adress kräver uppdatering av A-poster.
* förbrukar externa webbtjänster som använder IP-baserade säkerhetsmodell.
* använder SSL-certifikat som är kopplade till en IP-adress.

> [!NOTE]
> När du skapar en klassisk virtuell dator, en behållare *molntjänst* har skapats av Azure, som har en virtuell IP-adress (VIP). När du är klar att skapa via portalen, en standard RDP eller SSH *endpoint* har konfigurerats av portalen så att du kan ansluta till den virtuella datorn via cloud service-VIP. Den här cloud service-VIP kan reserveras, vilket ger en reserverad IP-adress för att ansluta till den virtuella datorn. Du kan öppna ytterligare portar genom att konfigurera fler slutpunkter.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Virtuella IaaS-datorer och PaaS-rollinstanser
Du kan tilldela en offentlig IP-adress direkt till ett IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller PaaS-rollinstans i en molntjänst. Detta kallas en offentlig IP-adress för på instansnivå ([ILPIP](virtual-networks-instance-level-public-ip.md)). Den här offentliga IP-adressen kan bara vara dynamisk.

> [!NOTE]
> Detta skiljer sig från VIP för Molntjänsten, vilket är en behållare för virtuella IaaS-datorer eller PaaS rollinstanser, eftersom en molntjänst kan innehålla flera virtuella IaaS-datorer och PaaS-rollinstanser, alla exponeras via samma cloud service VIP.
> 
> 

### <a name="vpn-gateways"></a>VPN-gateways
En [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) kan användas för att ansluta ett virtuellt Azure nätverk till andra virtuella Azure-nätverk eller i lokala nätverk. En VPN-gateway är tilldelad offentlig IP-adress *dynamiskt*, vilket gör att kommunicera med fjärrnätverket.

### <a name="application-gateways"></a>Programgateways
En Azure [Programgateway](../application-gateway/application-gateway-introduction.md) kan användas för Layer7-belastningsutjämning att dirigera nätverkstrafik baserat på HTTP. Application gateway är tilldelad offentlig IP-adress *dynamiskt*, som fungerar som den belastningsutjämnade VIP.

### <a name="at-a-glance"></a>En snabbtitt
Tabellen nedan visar varje resurstyp med nivån metoder (dynamiska/statiska) och möjligheten att tilldela flera offentliga IP-adresser.

| Resurs | Dynamisk | Statisk | Flera IP-adresser |
| --- | --- | --- | --- |
| Molntjänst |Ja |Ja |Ja |
| IaaS-VM eller PaaS rollinstans |Ja |Nej |Nej |
| VPN gateway |Ja |Nej |Nej |
| Programgateway |Ja |Nej |Nej |

## <a name="private-ip-addresses"></a>Privata IP-adresser
Privata IP-adresser gör att Azure-resurser kan kommunicera med andra resurser i en molntjänst eller en [virtuellt nätverk](virtual-networks-overview.md)(VNet), eller till lokala nätverk (via en VPN-gateway eller ExpressRoute-krets), utan att använda en Internet-ansluten IP-adress.

I Azure klassiska distributionsmodellen, kan en privat IP-adress tilldelas till följande Azure-resurser:

* Virtuella IaaS-datorer och PaaS-rollinstanser
* Intern lastbalanserare
* Programgateway

### <a name="iaas-vms-and-paas-role-instances"></a>Virtuella IaaS-datorer och PaaS-rollinstanser
Virtuella datorer (VM) som skapats med den klassiska distributionsmodellen placeras alltid i en molntjänst som liknar PaaS-rollinstanser. Beteendet för privata IP-adresser är därför liknande för dessa resurser.

Det är viktigt att Observera att en tjänst i molnet kan distribueras på två sätt:

* Som en *fristående* Molntjänsten, om det inte är inom ett virtuellt nätverk.
* Som en del av ett virtuellt nätverk.

#### <a name="allocation-method"></a>Allokeringsmetod
I händelse av en *fristående* Molntjänsten, resurser get en privat IP-adress allokeras *dynamiskt* från Azure-datacenter privata IP-adressintervall. Det kan användas enbart för kommunikation med andra virtuella datorer i samma molntjänst. Den här IP-adressen kan ändras när resursen stoppas och startas.

Vid en molntjänst som distribuerats i ett virtuellt nätverk, hämta resurser privata IP-adresser allokeras från adressintervallet för det associerade undernät (som anges i nätverkskonfigurationen). Den här privata IP-adresser kan användas för kommunikation mellan alla virtuella datorer inom det virtuella nätverket.

Dessutom vid molntjänster inom ett virtuellt nätverk, en privat IP-adress tilldelas *dynamiskt* (via DHCP) som standard. Den kan ändras när resursen stoppas och startas. För att säkerställa IP-adressen förblir densamma, måste du ange allokeringsmetoden *Statiska*, och ange en giltig IP-adress inom det motsvarande-adressintervallet.

Statiska privata IP-adresser används ofta för:

* Virtuella datorer som fungerar som domänkontrollanter eller DNS-servrar.
* Virtuella datorer som kräver brandväggsregler med IP-adresser.
* Virtuella datorer som kör tjänster som används av andra appar via en IP-adress.

#### <a name="internal-dns-hostname-resolution"></a>Intern DNS-värdnamnsmatchning
Alla virtuella Azure-datorer och PaaS-rollinstanser som är konfigurerade med [Azure-hanterade DNS-servrar](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard om du inte uttryckligen konfigurerar anpassade DNS-servrar. Dessa DNS-servrar tillhandahåller intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk eller i molnet-tjänsten.

När du skapar en virtuell dator läggs en mappning till för värdnamnet till dess privata IP-adress för de Azure-hanterade DNS-servrarna. Med virtuell dator med flera nätverkskort mappas värdnamnet till den privata IP-adressen för det primära nätverkskortet. Den här mappningsinformationen är dock begränsade resurser inom samma molntjänst eller virtuella nätverk.

I händelse av en *fristående* Molntjänsten, du kan matcha värdnamnen för alla virtuella datorer/rollinstanser i bara samma molntjänst. Vid en molntjänst i ett virtuellt nätverk, kommer du att kan matcha värdnamnen för alla virtuella datorer/rollinstanserna inom det virtuella nätverket.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interna lastbalanserare (ILB) och programgateways
Du kan tilldela en privat IP-adress till konfigurationen på **klientsidan** för en [intern lastbalanserare i Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) eller till en [programgateway i Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Den här privata IP-adressen fungerar som en intern slutpunkt som bara kan nås av resurserna i dess virtuella nätverk (VNet) och fjärrnätverken som är anslutna till det virtuella nätverket. Du kan tilldela antingen en dynamisk eller privat IP-adress till konfigurationen på klientsidan. Du kan också tilldela flera privata IP-adresser för att möjliggöra multi-vip-scenarier.

### <a name="at-a-glance"></a>En snabbtitt
Tabellen nedan visar varje resurstyp med nivån metoder (dynamiska/statiska) och möjligheten att tilldela flera privata IP-adresser.

| Resurs | Dynamisk | Statisk | Flera IP-adresser |
| --- | --- | --- | --- |
| Virtuell dator (i en *fristående* molntjänst eller virtuella nätverk) |Ja |Ja |Ja |
| PaaS-rollinstans (i en *fristående* molntjänst eller virtuella nätverk) |Ja |Nej |Nej |
| Intern klientsidan belastningsutjämnare |Ja |Ja |Ja |
| Application gateway-klientdel |Ja |Ja |Ja |

## <a name="limits"></a>Begränsningar
Tabellen nedan visar de begränsningar på IP-adresser i Azure per prenumeration. Du kan [kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standardgränserna upp till de maximala gränserna utifrån dina affärsbehov.

|  | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser (dynamiska) |5 |kontakta supporten |
| Reserverade offentliga IP-adresser |20 |kontakta supporten |
| Offentlig VIP per distribution (Molntjänsten) |5 |kontakta supporten |
| Privat VIP (ILB) per distribution (Molntjänsten) |1 |1 |

Se till att läsa den fullständiga uppsättningen [nätverksgränser](../azure-subscription-service-limits.md#networking-limits) i Azure.

## <a name="pricing"></a>Prissättning
I de flesta fall är offentliga IP-adresser kostnadsfria. Det finns en nominell avgift för att använda ytterligare och/eller statiska offentliga IP-adresser. Kontrollera att du förstår de [prisstrukturen för offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Skillnader mellan Resource Manager och klassiska distributioner
Nedan visas en jämförelse av IP-adressering funktioner i Resource Manager och den klassiska distributionsmodellen.

|  | Resurs | Klassisk | Resource Manager |
| --- | --- | --- | --- |
| **Offentlig IP-adress** |***VIRTUELL DATOR*** |Kallas en ILPIP (dynamisk) |Kallas en offentlig IP-adress (dynamisk eller statisk) |
|  ||Tilldelad till en IaaS-VM eller en PaaS-rollinstans |Som är kopplad till den Virtuella datorns nätverkskort | |
|  |***Internetuppkopplad belastningsutjämnare*** |Kallas VIP (dynamisk) eller reserverad IP-adress (statisk) |Kallas en offentlig IP-adress (dynamisk eller statisk) | |
|  ||Tilldelad till en molntjänst |Som är kopplad till belastningsutjämnarens klientdel config | |
|  | | | |
| **Privata IP-adress** |***VIRTUELL DATOR*** |Kallas en DIP |Kallas en privat IP-adress |
|  ||Tilldelad till en IaaS-VM eller en PaaS-rollinstans |Tilldelad till den Virtuella datorns nätverkskort | |
|  |***Intern belastningsutjämnare (ILB)*** |Tilldelad till den interna Belastningsutjämnaren (dynamisk eller statisk) |Tilldelad till den interna belastningsutjämnarens konfiguration på klientsidan (dynamisk eller statisk) | |

## <a name="next-steps"></a>Nästa steg
* [Distribuera en virtuell dator med en statisk privat IP-adress](virtual-networks-static-private-ip-classic-pportal.md) med Azure portal.

