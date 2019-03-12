---
title: IP-adresstyper i Azure
titlesuffix: Azure Virtual Network
description: Läs mer om offentliga och privata IP-adresser i Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: jdial
ms.openlocfilehash: 5240d6d4cd510eb4ea87392421fc00ae01b5e600
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771971"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP-adresstyper och allokeringsmetoder i Azure

Du kan tilldela IP-adresser till Azure-resurser för att kommunicera med andra Azure-resurser, det lokala nätverket och Internet. Det finns två typer av IP-adresser som du kan använda i Azure:

* **Offentliga IP-adresser**: Används för kommunikation med Internet, inklusive offentliga Azure-tjänster.
* **Privata IP-adresser**: Används för kommunikation inom ett virtuellt Azure-nätverk (VNet), och ditt lokala nätverk om du använder en VPN-gateway eller ExpressRoute-krets för att utöka ditt nätverk till Azure.

Du kan också skapa ett sammanhängande intervall med statiska offentliga IP-adresser via ett offentligt IP-prefix. [Läs mer om offentliga IP-prefix.](public-ip-address-prefix.md)

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för [den klassiska distributionsmodellen](virtual-network-ip-addresses-overview-classic.md).
> 

Om du är bekant med den klassiska distributionsmodellen läser du artikeln om [skillnaderna i IP-adressering mellan den klassisk modellen och Resource Manager](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Offentliga IP-adresser

Offentliga IP-adresser tillåter att Internet-resurser kommunicerar inkommande till Azure-resurser. Offentliga IP-adresser gör det också möjligt för Azure-resurser att kommunicera utgående till Internet och offentliga Azure-tjänster med en IP-adress som tilldelats resursen. Adressen är dedikerad till resursen tills du tar bort den. Om ingen offentlig IP-adress har tilldelats resursen kan resursen ändå kommunicera utgående till Internet, men Azure tilldelar dynamiskt en tillgänglig IP-adress som inte är dedikerad till resursen. Mer information om utgående anslutningar i Azure finns i [Förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

I Azure Resource Manager är en [offentlig IP-adress](virtual-network-public-ip-address.md) en resurs som har sina egna egenskaper. Följande är några av resurserna du kan associera med en offentlig IP-adressresurs:

* Nätverksgränssnitt för virtuella datorer
* Internetuppkopplade lastbalanserare
* VPN-gateways
* Programgateways

### <a name="ip-address-version"></a>IP-adressversion

Offentliga IP-adresser skapas med en IPv4- eller IPv6-adress. Offentliga IPv6-adresser kan endast tilldelas till internetuppkopplade lastbalanserare.

### <a name="sku"></a>SKU

Offentliga IP-adresser skapas med någon av följande SKU:er:

>[!IMPORTANT]
> Matchande SKU:er måste användas för lastbalanseraren och offentliga IP-resurser. Du kan inte blanda grundläggande SKU-resurser och standard-SKU-resurser. Du kan inte bifoga fristående virtuella datorer, virtuella datorer i en tillgänglighetsuppsättningsresurs eller en virtuell dators skalningsuppsättningsresurser till båda SKU:erna samtidigt.  Ny design bör överväga att använda standard-SKU-resurser.  Mer information finns i [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="basic"></a>Basic

Alla offentliga IP-adresser som skapas före införandet av SKU:er är grundläggande offentliga IP-adresser för SKU. Genom att införa SKU:er kan du välja att ange vilken SKU du vill att den offentliga IP-adressen ska vara. Grundläggande SKU-adresser är:

- Tilldelas med en statisk eller dynamisk allokeringsmetod.
- Har en justerbar inkommande tidsgräns för inaktivitet i flöde på 4–30 minuter, med ett standardvärde på 4 minuter, och en fast utgående tidsgräns för inaktivitet i flöde på 4 minuter.
- Är öppna som standard.  Nätverkssäkerhetsgrupper rekommenderas, men är valfritt för att begränsa inkommande eller utgående datatrafik.
- Tilldelas till en Azure-resurs som kan tilldelas en offentlig IP-adress, t.ex. nätverksgränssnitt, VPN Gateway, Application Gateway och Internetuppkopplade lastbalanserare.
- Stöd inte scenarier med tillgänglighetszoner.  Du behöver använda en standardmässig offentlig SKU-IP-adress för scenarier med tillgänglighetszoner. Om du vill veta mer om tillgänglighetszoner kan du läsa [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Standard Load Balancer och tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standard

Offentliga IP-adresser för standard-SKU:

- Använd alltid den statiska allokeringsmetoden.
- Har en justerbar inkommande tidsgräns för inaktivitet i flöde på 4–30 minuter, med ett standardvärde på 4 minuter, och en fast utgående tidsgräns för inaktivitet i flöde på 4 minuter.
- Är säkra som standard och stängda för inkommande trafik. Du måste explicit göra en lista över tillåten inkommande trafik med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups).
- Tilldelat till nätverksgränssnitt, offentliga Standard Load Balancers, programgatewayer eller VPN-gatewayer. Mer information om Standard Load Balancer finns i [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Zonredundant som standard och valfritt zonindelad (kan skapas zonindelad och garanteras i en specifik tillgänglighetszon). Om du vill veta mer om tillgänglighetszoner kan du läsa [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Standard Load Balancer och tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Inkommande kommunikation med en resurs med standard-SKU misslyckas tills du har skapat och kopplat en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups) och uttryckligen tillåtit önskad inkommande trafik.

### <a name="allocation-method"></a>Allokeringsmetod

Både grundläggande och standard-SKU:er för offentliga IP-adresser stöder den *statiska* allokeringsmetoden.  Resursen tilldelas en IP-adress när den skapas, och IP-adressen släpps när resursen tas bort.

Grundläggande SKU:er för offentliga IP-adresser stöder även en *dynamisk* allokeringsmetod, som är standard om ingen allokeringsmetod anges.  Om du väljer *dynamisk* allokeringsmetod för en grundläggande offentlig IP-adressresurs innebär att IP-adressen **inte** allokeras när resursen skapas.  Den offentliga IP-adressen allokeras när du tilldelar den offentliga IP-adressen med en virtuell dator eller när du placerar den första virtuella datorinstansen i serverdelspoolen för en grundläggande lastbalanserare.   IP-adressen frisläpps när du stoppar (eller tar bort) resursen.  När IP-adressen exempelvis har släppts från resurs A kan IP-adressen tilldelas till en annan resurs. Om IP-adressen tilldelas till en annan resurs medan resurs A är stoppad kommer en annan IP-adress att tilldelas när du startar om resurs A. Om du byter allokeringsmetod för en grundläggande offentlig IP-adressresurs från *statisk* till *dynamisk* släpps adressen. Om du inte vill att IP-adressen för den associera resursen ska ändras kan du uttryckligen ange allokeringsmetoden till *statisk*. En statisk IP-adress tilldelas direkt.

> [!NOTE]
> Även om du anger allokeringsmetoden till *statisk* kan du inte ange själva IP-adressen som tilldelas till den offentliga IP-adressresursen. Azure tilldelar i stället IP-adressen från en pool med tillgängliga IP-adresser på den Azure-plats där resursen skapas.
>

Statiska offentliga IP-adresser används ofta i följande scenarier:

* När du måste uppdatera brandväggsregler för att kommunicera med dina Azure-resurser.
* DNS-namnmatchning, där en ändring i IP-adressen kräver uppdatering av A-poster.
* Dina Azure-resurser kommunicerar med andra appar eller tjänster som använder en IP-adressbaserad säkerhetsmodell.
* Du kan använda SSL-certifikat som är kopplade till en IP-adress.

> [!NOTE]
> Azure allokerar offentliga IP-adresser från ett intervall som är unikt för varje region i varje Azure-moln. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).
>

### <a name="dns-hostname-resolution"></a>Matchning av DNS-värdnamn
Du kan ange en DNS-domännamnsetikett för en offentlig IP-resurs, vilket skapar en mappning för *domainnamelabel*.*location*. cloudapp.azure.com till den offentliga IP-adressen på de Azure-hanterade DNS-servrarna. Om du till exempel skapar en offentlig IP-resurs med **contoso** som *domainnamelabel* och väljer *Azure-platsen* **USA, västra**, så matchas det fullständigt kvalificerade domännamnet (FQDN) **contoso.westus.cloudapp.azure.com** till resursens offentliga IP-adress. Du kan använda detta fullständiga domännamn för att skapa en anpassad CNAME-domänpost som pekar på den offentliga IP-adressen i Azure. Istället för att använda DNS-namnetiketten med standardsuffixet, eller som ett tillägg till det, kan du använda Azure DNS-tjänsten för att konfigurera ett DNS-namn med ett anpassat suffix som motsvarar den offentliga IP-adressen. Mer information finns i [Use Azure DNS with an Azure public IP address](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) (Använda Azure DNS med en offentlig IP-adress för Azure).

> [!IMPORTANT]
> Varje domännamnsetikett som skapas måste vara unik inom dess Azure-plats.  
>

### <a name="virtual-machines"></a>Virtuella datorer

Du kan associera en offentlig IP-adress med en [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-baserad virtuell dator genom att tilldela den till dess **nätverksgränssnitt**. Du kan tilldela antingen en dynamisk eller en statisk offentlig IP-adress till en virtuell dator. Lär dig mer om att [tilldela IP-adresser till nätverksgränssnitt](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Internetuppkopplade lastbalanserare

Du kan associera en offentlig IP-adress som skapas med en [SKU](#SKU) till en [Azure Load Balancer](../load-balancer/load-balancer-overview.md) genom att tilldela den till lastbalanserarens konfiguration på **klientsidan**. Den offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress (VIP). Du kan tilldela antingen en dynamisk eller en statisk offentlig IP-adress till klientsidan för en lastbalanserare. Du kan också tilldela flera offentliga IP-adresser till klientsidan för en lastbalanserare, t.ex. för scenarier med [flera virtuella IP-adresser](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), exempelvis i en miljö med flera klientorganisationer med SSL-baserade webbplatser. Mer information om SKU:er för lastbalanserare i Azure finns i [Standard-SKU för Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="vpn-gateways"></a>VPN-gateways

[VPN Gateway i Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ansluter ett virtuellt Azure-nätverk (VNet) till andra virtuella Azure-nätverk eller till ett lokalt nätverk. En offentlig IP-adress tilldelas till VPN Gateway så att den kan kommunicera med fjärrnätverket. Du kan endast tilldela en *dynamisk* grundläggande offentlig IP-adress till en VPN-gateway.

### <a name="application-gateways"></a>Programgateways

Du kan associera en offentlig IP-adress med en Azure [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genom att tilldela den till gatewayens konfiguration på **klientsidan**. Den här offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress. Du kan endast tilldela en *dynamisk* grundläggande offentlig IP-adress till en programgateways konfiguration på klientsidan för V1 och endast en statisk, grundläggande eller SKU-standardadress till en V2-konfiguration på klientsidan.

### <a name="at-a-glance"></a>En snabb översikt
Följande tabell visar den specifika egenskapen som kan användas för att associera en offentlig IP-adress till en resurs på den översta nivån, samt de metoder (dynamisk eller statisk) som kan användas.

| Resurs på den översta nivån | IP-adressassociation | Dynamisk | Statisk |
| --- | --- | --- | --- |
| Virtuell dator |Nätverksgränssnitt |Ja |Ja |
| Internetuppkopplad lastbalanserare |Konfiguration på klientsidan |Ja |Ja |
| VPN gateway |IP-konfiguration för gateway |Ja |Ja |
| Programgateway |Konfiguration på klientsidan |Ja (endast V1) |Ja (endast V2) |

## <a name="private-ip-addresses"></a>Privata IP-adresser
Privata IP-adresser gör att Azure-resurser kan kommunicera med andra resurser i ett [virtuellt nätverk](virtual-networks-overview.md) eller i ett lokalt nätverk via en VPN-gateway eller ExpressRoute-krets utan att en IP-adress som kan nås över Internet används.

I Azure Resource Manager-distributionsmodellen associeras en privat IP-adress med följande typer av Azure-resurser:

* Nätverksgränssnitt för virtuella datorer
* Interna lastbalanserare (ILB)
* Programgateways

### <a name="ip-address-version"></a>IP-adressversion

Privata IP-adresser skapas med en IPv4- eller IPv6-adress. Privata IPv6-adresser kan endast tilldelas med den dynamiska allokeringsmetoden. Du kan inte kommunicera mellan privata IPv6-adresser i ett virtuellt nätverk. Du kan kommunicera inkommande till en privat IPv6-adress från Internet via en internetuppkopplad lastbalanserare. Mer information finns i [Skapa en lastbalanserare mot Internet med IPv6 (CLI)](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="allocation-method"></a>Allokeringsmetod

En privat IP-adress allokeras från adressintervallet för det virtuella undernätet som en resurs är distribuerad i. Azure reserverar de fyra första adresserna i varje undernäts adressintervall, så att adresserna inte kan tilldelas till resurser. Om undernätets adressintervall till exempel är 10.0.0.0/16 kan inte adresserna 10.0.0.0-10.0.0.3 tilldelas resurser. IP-adresser inom undernätets adressintervall kan endast tilldelas en resurs åt gången. 

En privat IP-adress kan allokeras med två metoder:

- **Dynamisk**: Azure tilldelar nästa tillgängliga otilldelade eller oreserverade IP-adress i undernätets adressintervall. Azure tilldelar exempelvis 10.0.0.10 till en ny resurs om adresserna 10.0.0.4-10.0.0.9 redan är tilldelade andra resurser. Dynamisk är standardinställningen för allokering. När de har tilldelats släpps endast dynamiska IP-adresser om ett nätverksgränssnitt har tagits bort, tilldelats ett annat undernät inom samma virtuella nätverk eller om allokeringsmetoden ändras till statisk och en annan IP-adress anges. Som standard tilldelar Azure den tidigare dynamiskt tilldelade adressen som statisk adress när du ändrar allokeringsmetod från dynamisk till statisk.
- **Statisk**: Du väljer och tilldelar en otilldelad eller oreserverad IP-adress i undernätets adressintervall. Om adressintervallet för ett undernät till exempel är 10.0.0.0/16 och adresserna 10.0.0.4–10.0.0.9 redan har tilldelats andra resurser kan du tilldela alla adresser mellan 10.0.0.10–10.0.255.254. Statiska adresser släpps endast om ett nätverksgränssnitt tas bort. Om du ändrar allokeringsmetoden till dynamisk tilldelar Azure dynamiskt de tidigare tilldelade statiska IP-adresserna som dynamiska adresser, även om adressen inte är nästa tillgängliga adress i undernätets adressintervall. Adressen ändras också om nätverksgränssnittet har tilldelats ett annat undernät i samma virtuella nätverk. För att tilldela nätverksgränssnittet till ett annat undernät måste du emellertid först ändra allokeringsmetoden från statisk till dynamisk. När nätverksgränssnittet har tilldelats ett annat undernät kan du ändra allokeringsmetod till statisk och tilldela en IP-adress från det nya undernätets adressintervall.

### <a name="virtual-machines"></a>Virtuella datorer

En eller flera privata IP-adresser tilldelas till ett eller flera **nätverksgränssnitt** för en [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-baserad virtuell dator. Du kan ange allokeringsmetoden som dynamisk eller statisk för varje privat IP-adress.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Intern DNS-värdnamnsmatchning (för virtuella datorer)

Alla virtuella datorer i Azure konfigureras med [Azure-hanterade DNS-servrar](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard, om du inte uttryckligen konfigurerar anpassade DNS-servrar. Dessa DNS-servrar tillhandahåller intern namnmatchning för virtuella datorer som finns i samma virtuella nätverk.

När du skapar en virtuell dator läggs en mappning till för värdnamnet till dess privata IP-adress för de Azure-hanterade DNS-servrarna. Om en virtuell dator har flera nätverksgränssnitt, eller flera IP-konfigurationer för ett nätverksgränssnitt, mappas värdnamnet till den privata IP-adressen för den primära IP-konfigurationen av det primära nätverksgränssnittet.

Virtuella datorer som konfigurerats med Azure-hanterade DNS-servrar kan matcha värdnamnen för alla virtuella datorer i samma virtuella nätverk till sina privata IP-adresser. Du måste använda en anpassad DNS-server för att matcha värdnamn på virtuella datorer i anslutna virtuella nätverk.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interna lastbalanserare (ILB) och programgateways

Du kan tilldela en privat IP-adress till konfigurationen på **klientsidan** för en [intern lastbalanserare i Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) eller till en [programgateway i Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här privata IP-adressen fungerar som en intern slutpunkt som bara kan nås av resurserna i dess virtuella nätverk och fjärrnätverken som är anslutna till det virtuella nätverket. Du kan tilldela antingen en dynamisk eller privat IP-adress till konfigurationen på klientsidan.

### <a name="at-a-glance"></a>En snabb översikt
Följande tabell visar den specifika egenskapen som kan användas för att associera en privat IP-adress till en resurs på den översta nivån, samt de metoder (dynamisk eller statisk) som kan användas.

| Resurs på den översta nivån | IP-adressassociation | Dynamisk | Statisk |
| --- | --- | --- | --- |
| Virtuell dator |Nätverksgränssnitt |Ja |Ja |
| Lastbalanserare |Konfiguration på klientsidan |Ja |Ja |
| Programgateway |Konfiguration på klientsidan |Ja |Ja |

## <a name="limits"></a>Begränsningar
Gränserna som gäller för IP-adressering anges i den fullständiga förteckningen över [nätverksgränserna](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) i Azure. Gränserna anges per region och per prenumeration. Du kan [kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standardgränserna upp till de maximala gränserna utifrån dina affärsbehov.

## <a name="pricing"></a>Prissättning
Offentliga IP-adresser kan medföra en nominell avgift. Mer information om priserna för IP-adresser i Azure finns på sidan med [priser för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Nästa steg
* [Distribuera en virtuell dator med en statisk offentlig IP-adress i Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
* [Distribuera en virtuell dator med en statisk privat IP-adress med hjälp av Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
