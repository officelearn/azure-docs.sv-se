---
title: IP-adresstyper i Azure | Microsoft Docs
description: "Läs mer om offentliga och privata IP-adresser i Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0506de98119576aae8f55cc6067f0bee95334c87
ms.lasthandoff: 03/31/2017


---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP-adresstyper och allokeringsmetoder i Azure
Du kan tilldela IP-adresser till Azure-resurser för att kommunicera med andra Azure-resurser, det lokala nätverket och Internet. Det finns två typer av IP-adresser som du kan använda i Azure:

* **Offentliga IP-adresser**: Används för kommunikation med Internet, inklusive offentliga Azure-tjänster.
* **Privata IP-adresser**: Används för kommunikation inom ett virtuellt Azure-nätverk (VNet), och ditt lokala nätverk om du använder en VPN-gateway eller ExpressRoute-krets för att utöka ditt nätverk till Azure.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md).  Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för [den klassiska distributionsmodellen](virtual-network-ip-addresses-overview-classic.md).
> 

Om du är bekant med den klassiska distributionsmodellen läser du artikeln om [skillnaderna i IP-adressering mellan den klassisk modellen och Resource Manager](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Offentliga IP-adresser
Offentliga IP-adresser gör att Azure-resurser kan kommunicera med Internet och offentliga Azure-tjänster som [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL Database](../sql-database/sql-database-technical-overview.md) och [Azure Storage](../storage/storage-introduction.md).

I Azure Resource Manager är en [offentlig IP-adress](resource-groups-networking.md#public-ip-address) en resurs som har sina egna egenskaper. Du kan associera en offentlig IP-adressresurs med någon av följande resurser:

* Virtuella datorer (VM)
* Internetuppkopplade belastningsutjämnare
* VPN-gateways
* Programgateways

### <a name="allocation-method"></a>Allokeringsmetod
En IP-adress kan tilldelas till en *offentlig* IP-resurs med två metoder: *dynamisk* eller *statisk* allokering. Standardallokeringsmetoden är *dynamisk*, där IP-adressen **inte** tilldelas när den skapas. I stället tilldelas den offentliga IP-adressen när du startar (eller skapar) den associerade resursen (t.ex. en virtuell dator eller belastningsutjämnare). IP-adressen frisläpps när du stoppar (eller tar bort) resursen. Med den här metoden ändras IP-adressen när du stoppar och startar en resurs.

Om du inte vill att IP-adressen för den associera resursen ska ändras kan du uttryckligen ange allokeringsmetoden till *statisk*. I så fall tilldelas en IP-adress direkt. Den frisläpps bara om du ta bort resursen eller om du ändrar dess allokeringsmetod till *dynamisk*.

> [!NOTE]
> Även om du anger allokeringsmetoden till *statisk* kan du inte ange själva IP-adressen som tilldelas till den *offentliga IP-resursen*. I stället tilldelas den från en pool med tillgängliga IP-adresser på den Azure-plats där resursen skapas.
>

Statiska offentliga IP-adresser används ofta i följande scenarier:

* Slutanvändarna måste uppdatera brandväggsregler för att kommunicera med dina Azure-resurser.
* DNS-namnmatchning, där en ändring i IP-adressen kräver uppdatering av A-poster.
* Dina Azure-resurser kommunicerar med andra appar eller tjänster som använder en IP-adressbaserad säkerhetsmodell.
* Du kan använda SSL-certifikat som är kopplade till en IP-adress.

> [!NOTE]
> Listan över IP-adressintervall som används för att tilldela offentliga IP-adresser (dynamiska/statiska) till Azure-resurser finns i avsnittet om [IP-intervall för Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>Matchning av DNS-värdnamn
Du kan ange en DNS-domännamnsetikett för en offentlig IP-resurs, vilket skapar en mappning för *domainnamelabel*.*location*. cloudapp.azure.com till den offentliga IP-adressen på de Azure-hanterade DNS-servrarna. Om du till exempel skapar en offentlig IP-resurs med **contoso** som *domainnamelabel* och väljer *Azure-platsen* **USA, västra**, så matchas det fullständigt kvalificerade domännamnet (FQDN) **contoso.westus.cloudapp.azure.com** till resursens offentliga IP-adress. Du kan använda detta fullständiga domännamn för att skapa en anpassad CNAME-domänpost som pekar på den offentliga IP-adressen i Azure.

> [!IMPORTANT]
> Varje domännamnsetikett som skapas måste vara unik inom dess Azure-plats.  
>

### <a name="virtual-machines"></a>Virtuella datorer
Du kan associera en offentlig IP-adress med en [Windows](../virtual-machines/windows/about.md)- eller [Linux](../virtual-machines/virtual-machines-linux-about.md)-baserad virtuell dator genom att tilldela den till dess **nätverksgränssnitt**. Om du har en virtuell dator med flera nätverkskort kan du bara koppla den till det *primära* nätverksgränssnittet. Du kan tilldela antingen en dynamisk eller en statisk offentlig IP-adress till en virtuell dator.

### <a name="internet-facing-load-balancers"></a>Internetuppkopplade belastningsutjämnare
Du kan associera en offentlig IP-adress med en [Azure Load Balancer-belastningsutjämnare](../load-balancer/load-balancer-overview.md) genom att tilldela den till belastningsutjämnarens konfiguration på **klientsidan**. Den här offentliga IP-adressen fungerar som en belastningsutjämnad virtuella IP-adress (VIP). Du kan tilldela antingen en dynamisk eller en statisk offentlig IP-adress till klientsidan för en belastningsutjämnare. Du kan också tilldela flera offentliga IP-adresser till klientsidan för en belastningsutjämnare, t.ex. för scenarier med [flera virtuella IP-adresser](../load-balancer/load-balancer-multivip.md), exempelvis i en miljö med flera klientorganisationer med SSL-baserade webbplatser.

### <a name="vpn-gateways"></a>VPN-gateways
[VPN-gateway i Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) används för att ansluta ett virtuellt Azure-nätverk (VNet) till andra virtuella Azure-nätverk eller till ett lokalt nätverk. Du måste tilldela en offentlig IP-adress till dess **IP-konfiguration** så att den kan kommunicera med fjärrnätverket. För närvarande kan du endast tilldela en *dynamisk* offentlig IP-adress till en VPN-gateway.

### <a name="application-gateways"></a>Programgateways
Du kan associera en offentlig IP-adress med en Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) genom att tilldela den till gatewayens konfiguration på **klientsidan**. Den här offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress. För närvarande kan du endast tilldela en *dynamisk* offentlig IP-adress till en programgateways konfiguration på klientsidan.

### <a name="at-a-glance"></a>En snabb översikt
Tabellen nedan visar den specifika egenskapen som kan användas för att associera en offentlig IP-adress till en resurs på den översta nivån, samt de metoder (dynamisk eller statisk) som kan användas.

| Resurs på den översta nivån | IP-adressassociation | Dynamisk | Statisk |
| --- | --- | --- | --- |
| Virtuell dator |Nätverksgränssnitt |Ja |Ja |
| Belastningsutjämnare |Konfiguration på klientsidan |Ja |Ja |
| VPN-gateway |IP-konfiguration för gateway |Ja |Nej |
| Programgateway |Konfiguration på klientsidan |Ja |Nej |

## <a name="private-ip-addresses"></a>Privata IP-adresser
Privata IP-adresser gör att Azure-resurser kan kommunicera med andra resurser i ett [virtuellt nätverk](virtual-networks-overview.md) eller i ett lokalt nätverk via en VPN-gateway eller ExpressRoute-krets utan att en IP-adress som kan nås över Internet används.

I Azure Resource Manager-distributionsmodellen associeras en privat IP-adress med följande typer av Azure-resurser:

* Virtuella datorer
* Interna belastningsutjämnare (ILB)
* Programgateways

### <a name="allocation-method"></a>Allokeringsmetod
En privat IP-adress tilldelas från adressintervallet för undernätet som resursen hör till. Adressintervallet för själva undernätet ingår i det virtuella nätverkets adressintervall.

En privat IP-adress kan allokeras med två metoder: *dynamisk* eller *statisk* allokering. Standardallokeringsmetoden är *dynamisk*, där IP-adressen tilldelas automatiskt från resursens undernät (via DHCP). Den här IP-adressen kan ändras när du stoppar och startar resursen.

Du kan ange allokeringsmetoden till *statisk* om du inte vill att IP-adressen ska ändras. I så fall måste du även ange en giltig IP-adress som finns i resursens undernät.

Statiska privata IP-adresser används ofta för:

* Virtuella datorer som fungerar som domänkontrollanter eller DNS-servrar.
* Resurser som kräver brandväggsregler med IP-adresser.
* Resurser som nås av andra appar/resurser via en IP-adress.

### <a name="virtual-machines"></a>Virtuella datorer
En privat IP-adress tilldelas till **nätverksgränssnittet** för en [Windows](../virtual-machines/windows/about.md)- eller [Linux](../virtual-machines/virtual-machines-linux-about.md)-baserad virtuell dator. Om du har en virtuell dator med flera nätverksgränssnitt tilldelas varje gränssnitt en privat IP-adress. Du kan ange allokeringsmetoden som dynamisk eller statisk för ett nätverksgränssnitt.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Intern DNS-värdnamnsmatchning (för virtuella datorer)
Alla virtuella datorer i Azure konfigureras med [Azure-hanterade DNS-servrar](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard, om du inte uttryckligen konfigurerar anpassade DNS-servrar. Dessa DNS-servrar tillhandahåller intern namnmatchning för virtuella datorer som finns i samma virtuella nätverk (VNet).

När du skapar en virtuell dator läggs en mappning till för värdnamnet till dess privata IP-adress för de Azure-hanterade DNS-servrarna. Om du har en virtuell dator med flera nätverksgränssnitt mappas värdnamnet till den privata IP-adressen för det primära nätverksgränssnittet.

Virtuella datorer som konfigurerats med Azure-hanterade DNS-servrar kan matcha värdnamnen för alla virtuella datorer i deras VNet till sina privata IP-adresser.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interna belastningsutjämnare (ILB) och programgateways
Du kan tilldela en privat IP-adress till konfigurationen på **klientsidan** för en [intern belastningsutjämnare i Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) eller till en [programgateway i Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Den här privata IP-adressen fungerar som en intern slutpunkt som bara kan nås av resurserna i dess virtuella nätverk (VNet) och fjärrnätverken som är anslutna till det virtuella nätverket. Du kan tilldela antingen en dynamisk eller privat IP-adress till konfigurationen på klientsidan.

### <a name="at-a-glance"></a>En snabb översikt
Tabellen nedan visar den specifika egenskapen som kan användas för att associera en privat IP-adress till en resurs på den översta nivån, samt de metoder (dynamisk eller statisk) som kan användas.

| Resurs på den översta nivån | IP-adressassociation | Dynamisk | Statisk |
| --- | --- | --- | --- |
| Virtuell dator |Nätverksgränssnitt |Ja |Ja |
| Belastningsutjämnare |Konfiguration på klientsidan |Ja |Ja |
| Programgateway |Konfiguration på klientsidan |Ja |Ja |

## <a name="limits"></a>Begränsningar
Gränserna som gäller för IP-adressering anges i den fullständiga förteckningen över [nätverksgränserna](../azure-subscription-service-limits.md#networking-limits) i Azure. Dessa gränser anges per region och per prenumeration. Du kan [kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standardgränserna upp till de maximala gränserna utifrån dina affärsbehov.

## <a name="pricing"></a>Priser
Offentliga IP-adresser kan medföra en nominell avgift. Mer information om priserna för IP-adresser i Azure finns på sidan med [priser för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Nästa steg
* [Distribuera en virtuell dator med en statisk offentlig IP-adress i Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
* [Distribuera en virtuell dator med en statisk offentlig IP-adress med hjälp av en mall](virtual-network-deploy-static-pip-arm-template.md)
* [Distribuera en virtuell dator med en statisk privat IP-adress med hjälp av Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)

