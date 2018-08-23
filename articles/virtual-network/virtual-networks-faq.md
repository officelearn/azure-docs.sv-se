---
title: Azure-nätverk vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om Microsoft Azure-nätverk.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: 2802a725bca7f63f6956293048b0e854ebfb59b5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061124"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Vanliga frågor (och svar FAQ) om Azure-nätverk

## <a name="virtual-network-basics"></a>Grunderna i virtuella nätverk

### <a name="what-is-an-azure-virtual-network-vnet"></a>Vad är Azure Virtual Network (VNet)?
Azure Virtual Network (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan använda virtuella nätverk kan etablera och hantera virtuella privata nätverk (VPN) i Azure och, om du vill länka virtuella nätverk med andra virtuella nätverk i Azure, eller med din lokala IT-infrastruktur för att skapa hybrid- eller mellan lokala lösningar. Varje virtuellt nätverk som du skapar har sin egen CIDR-block och kan bara kopplas till andra virtuella nätverk och lokala nätverk som inte överlappar varandra CIDR-block. Du kan ha kontroll över DNS-serverinställningarna för virtuella nätverk och segmentering av VNet i undernät.

Använd virtuella nätverk till:

* Skapa en dedikerad privat molnbaserad VNet ibland du inte behöver en konfiguration för flera platser för din lösning. När du skapar ett virtuellt nätverk, kan dina tjänster och virtuella datorer i ditt virtuella nätverk kommunicera direkt och säkert med varandra i molnet. Du kan fortfarande konfigurera slutpunkt-anslutningar för virtuella datorer och tjänster som kräver Internet-kommunikation som en del av din lösning.
* Utöka ditt datacenter med virtuella nätverk, kan du skapa traditionella plats-till-plats (S2S) VPN: er för att på ett säkert sätt skala datacentrets kapacitet. S2S VPN använda IPSEC för att tillhandahålla en säker anslutning mellan ditt företags VPN-gateway och Azure.
* Aktivera hybridmolnscenarierna virtuella nätverk ger dig flexibilitet och stöd för flera olika hybridmolnscenarierna. Du kan på ett säkert sätt ansluta molnbaserade program till alla typer av lokalt system, till exempel stordatorer och Unix-system.

### <a name="how-do-i-get-started"></a>Hur kommer jag igång?
Gå till den [dokumentation om Virtual network](https://docs.microsoft.com/azure/virtual-network/) att komma igång. Det här innehållet ger information om översikt och distribution för alla VNet-funktioner.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan jag använda virtuella nätverk utan anslutning på flera platser?
Ja. Du kan använda ett virtuellt nätverk utan att ansluta den till ditt lokala nätverk. Du kan till exempel köra Microsoft Windows Server Active Directory-domänkontrollanter och SharePoint-grupper i ett virtuellt Azure nätverk.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan jag utföra WAN-optimering mellan virtuella nätverk eller ett virtuellt nätverk och min lokala Datacenter?
Ja. Du kan distribuera en [WAN-optimering virtuell nätverksinstallation](https://azure.microsoft.com/marketplace/?term=wan+optimization) från flera leverantörer via Azure Marketplace.

## <a name="configuration"></a>Konfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Vilka verktyg kan jag använda för att skapa ett virtuellt nätverk?
Du kan använda följande verktyg för att skapa eller konfigurera ett virtuellt nätverk:

* Azure Portal
* PowerShell
* Azure CLI
* En nätverkskonfigurationsfil (netcfg - för klassiska virtuella nätverk endast). Se den [konfigurera ett virtuellt nätverk med en nätverkskonfigurationsfil](virtual-networks-using-network-configuration-file.md) artikeln.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Vilka adressintervall kan jag använda Mina virtuella nätverk?
Alla IP-adressintervall som definierats i [RFC 1918](http://tools.ietf.org/html/rfc1918). Till exempel 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan jag ha offentliga IP-adresser i mitt virtuella nätverk?
Ja. Mer information om offentliga IP-adressintervall finns i [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network). Offentliga IP-adresser kan inte nås direkt från internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Finns det en gräns för antalet undernät i mitt VNet?
Ja. Se [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) mer information. Adressutrymmen för undernätet får inte överlappa varandra.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Finns det några begränsningar med IP-adresser inom dessa undernät?
Ja. Azure reserverar vissa IP-adresser i varje undernät. De första och sista IP-adresserna för varje undernät är reserverade för protokollöverensstämmelse, tillsammans med de x.x.x.1 x.x.x.3 adresserna i varje undernät som används för Azure-tjänster.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hur små och hur stor kan vara virtuella nätverk och undernät?
Minsta stöds undernätet är /29 och den största är/8 som (med CIDR-subnätsdefinitioner).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan jag använda Mina virtuella lokala nätverk till Azure med hjälp av virtuella nätverk?
Nej. Virtuella nätverk är Layer 3-överlägg. Azure stöder inte någon Layer-2-semantik.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan jag ange anpassade routning-principer på mitt virtuella nätverk och undernät?
Ja. Du kan skapa en routningstabell och koppla den till ett undernät. Mer information om routning i Azure finns i [routningsöversikten](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Stöder virtuella nätverk multicast eller broadcast?
Nej. Multicast och Sänd stöds inte.

### <a name="what-protocols-can-i-use-within-vnets"></a>Vilka protokoll kan jag använda i virtuella nätverk?
Du kan använda TCP, UDP och ICMP TCP/IP-protokoll i virtuella nätverk. Unicast stöds i virtuella nätverk, med undantag för DHCP Dynamic Host Configuration Protocol () via Unicast (källport UDP/68 / mål port UDP/67). Multicast, broadcast, inkapslad IP-i-IP-paket och paket som Generic Routing Encapsulation (GRE) är blockerade i virtuella nätverk. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan jag Pinga standard-routrar i ett virtuellt nätverk?
Nej.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan jag använda tracert för att diagnostisera anslutning?
Nej.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan jag lägga till undernät när det virtuella nätverket har skapats?
Ja. Undernät kan läggas till virtuella nätverk när som helst så länge adressintervall för undernätet ingår inte i ett annat undernät och det finns tillgängligt utrymme kvar i det virtuella nätverkets adressintervall.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan jag ändra storleken på mitt undernät när jag har skapat den?
Ja. Du kan lägga till, ta bort, expandera eller förminska ett undernät om det finns inga virtuella datorer eller tjänster som är distribuerade i den.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Kan jag ändra undernät när jag skapar dem?
Ja. Du kan lägga till, ta bort och ändra CIDR-block som används av ett virtuellt nätverk.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Om jag kör Mina tjänster i ett virtuellt nätverk, kan jag ansluta till internet?
Ja. Alla tjänster som distribueras i ett virtuellt nätverk kan ansluta utgående till internet. Mer information om utgående internet-anslutningar i Azure finns [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill ansluta till inkommande till en resurs som distribueras via Resource Manager, måste resursen ha en offentlig IP-adress som tilldelats. Läs mer om offentliga IP-adresser i [offentliga IP-adresser](virtual-network-public-ip-address.md). Varje Azure-molntjänst som distribueras i Azure har ett offentligt adresserbara VIP som har tilldelats. Du kan definiera inkommande slutpunkter för PaaS-roller och slutpunkter för virtuella datorer att aktivera de här tjänsterna att godkänna anslutningar från internet.

### <a name="do-vnets-support-ipv6"></a>Virtuella nätverk som har stöd för IPv6?
Nej. Du kan inte använda IPv6 med virtuella nätverk just nu. Men du kan tilldela IPv6-adresser till Azure-belastningsutjämnare att läsa in belastningsutjämna virtuella datorer. Mer information finns i [översikt över IPv6 för Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Ett virtuellt nätverk kan omfatta regioner?
Nej. Ett virtuellt nätverk är begränsad till en enda region. Ett virtuellt nätverk gör det, men sträcker sig över tillgänglighetszoner. Mer information om tillgänglighetszoner finns i [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Du kan ansluta virtuella nätverk i olika regioner med peerkoppling. Mer information finns i [peering översikt över virtuella nätverk](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan jag ansluta ett virtuellt nätverk till ett annat virtuellt nätverk i Azure?
Ja. Du kan ansluta ett virtuellt nätverk till ett annat VNet med hjälp av antingen:
- **Virtuell nätverkspeering**: Mer information finns i [översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md)
- **Azure VPN Gateway**: Mer information finns i [konfigurera en VNet-till-VNet-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Namnmatchning (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Vad är DNS-alternativ för virtuella nätverk?
Använd tabellen beslut på den [namnmatchning för virtuella datorer och Rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md) sidan att guida dig genom alla DNS-alternativ tillgängliga.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan jag ange DNS-servrar för ett virtuellt nätverk?
Ja. Du kan ange DNS-IP-adresser i VNet-inställningarna. Inställningen tillämpas som standard-DNS-servrar för alla virtuella datorer i det virtuella nätverket.

### <a name="how-many-dns-servers-can-i-specify"></a>Hur många DNS-servrar kan jag välja?
Referens [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan jag ändra min DNS-servrar när jag har skapat nätverket?
Ja. Du kan ändra DNS-serverlistan för ditt virtuella nätverk när som helst. Om du ändrar DNS-serverlistan kommer du behöva starta om var och en av de virtuella datorerna i ditt virtuella nätverk för att hämta den nya DNS-servern.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Vad är Azure-tillhandahållna DNS och fungerar det med virtuella nätverk?
Azure-tillhandahållna DNS är en DNS-tjänst för flera innehavare som erbjuds av Microsoft. Azure registrerar alla dina virtuella datorer och rollinstanser för cloud service i den här tjänsten. Den här tjänsten tillhandahåller namnmatchning genom att värdnamnet för virtuella datorer och rollinstanser som finns i samma molntjänst och FQDN för virtuella datorer och rollinstanser i samma virtuella nätverk. Läs mer om DNS i [namnmatchning för virtuella datorer och molntjänster rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Det finns en begränsning för de första 100 molntjänsterna i ett virtuellt nätverk för flera klienter namnmatchning med hjälp av Azure-tillhandahållna DNS. Den här begränsningen gäller inte om du använder en egen DNS-server.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Kan jag göra egna DNS-inställningarna för tjänst per virtuell dator eller molntjänst?
Ja. Du kan ange DNS-servrar per virtuell dator eller molntjänst-tjänsten för att åsidosätta standardinställningarna för nätverk. Det rekommenderas dock att du använder DNS för hela nätverket så mycket som möjligt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan jag använda min egen DNS-suffix?
Nej. Du kan inte ange ett anpassat DNS-suffix för dina virtuella nätverk.

## <a name="connecting-virtual-machines"></a>Anslutning av virtuella datorer

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan jag distribuera virtuella datorer till ett virtuellt nätverk?
Ja. Alla nätverksgränssnitt (NIC) kopplade till en virtuell dator distribueras via Resource Manager-distributionsmodellen måste vara ansluten till ett virtuellt nätverk. Virtuella datorer distribueras via den klassiska distributionsmodellen kan också anslutas till ett virtuellt nätverk.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Vilka är de olika typerna av IP-adresser som jag kan tilldelas virtuella datorer?
* **Privat:** tilldelad till varje nätverkskort på varje virtuell dator. Adressen har tilldelats med hjälp av antingen metoden statisk eller dynamisk. Privata IP-adresser tilldelas från det adressintervall som du angav i Undernätverksinställningarna för för ditt VNet. Resurser som distribueras via den klassiska distributionsmodellen tilldelas privata IP-adresser, även om de inte är ansluten till ett virtuellt nätverk. Beteendet för allokeringsmetoden är olika beroende på om en resurs har distribuerats med Resource Manager eller klassiska distributionsmodellen: 

  - **Resource Manager**: en privat IP-adress med metoden dynamisk eller statisk fortfarande kopplad till en virtuell dator (Resource Manager) tills resursen tas bort. Skillnaden är att du väljer adressen som ska tilldelas när du använder statiskt och Azure väljer när du använder dynamisk. 
  - **Klassiska**: en privat IP-adress med metoden dynamisk kan ändras när en virtuell dator (klassisk) virtuell dator startas om efter att ha varit i tillståndet Stoppad (frigjord). Om du vill se till att den privata IP-adressen för en resurs som distribueras via den klassiska distributionsmodellen aldrig ändras kan du tilldela en privat IP-adress med statisk metod.

* **Offentliga:** eventuellt tilldelad till nätverkskort som är kopplade till virtuella datorer distribueras via Azure Resource Manager-distributionsmodellen. Adressen kan tilldelas med statisk eller dynamisk allokeringsmetod. Alla virtuella datorer och molntjänster rollinstanser som distribuerats via den klassiska distributionsmodellen finns i en molntjänst som är tilldelad en *dynamisk*, offentlig virtuell IP (VIP)-adress. En offentlig *Statiska* IP-adress, som kallas en [reserverad IP-adress](virtual-networks-reserved-public-ip.md), kan du kan också tilldelas som en VIP. Du kan tilldela offentliga IP-adresser till enskilda virtuella datorer eller molntjänster rollinstanser distribueras via den klassiska distributionsmodellen. De här adresserna kallas [instans på offentlig IP-adress (ILPIP](virtual-networks-instance-level-public-ip.md) -adresser och kan tilldelas dynamiskt.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan jag reservera en privat IP-adress för en virtuell dator som jag skapar vid ett senare tillfälle?
Nej. Du kan inte reservera en privat IP-adress. Om det finns en privat IP-adress tilldelas den till en virtuell dator eller rollen instans av DHCP-servern. Den virtuella datorn kanske eller kanske inte det som du vill tilldela privata IP-adress. Du kan dock ändra privata IP-adressen för en redan skapad VM till alla tillgängliga privata IP-adress.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Gör privat IP-adresser ändras för virtuella datorer i ett virtuellt nätverk?
Det beror på. Om den virtuella datorn har distribuerats via Resource Manager Nej, oavsett om IP-adress har tilldelats med statisk eller dynamisk allokeringsmetod. Om den virtuella datorn har distribuerats via den klassiska distributionsmodellen, kan dynamiska IP-adresser ändras när en virtuell dator startas efter att ha varit i tillståndet Stoppad (frigjord). Adressen frisläpps från en virtuell dator distribueras via antingen distributionsmodellen när den virtuella datorn tas bort.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan jag manuellt tilldela IP-adresser till nätverkskort i VM-operativsystem?
Ja, men det rekommenderas inte såvida inte behövs, till exempel när tilldela flera IP-adresser till en virtuell dator. Mer information finns i [att lägga till flera IP-adresser till en virtuell dator](virtual-network-multiple-ip-addresses-portal.md#os-config). Om IP-adress till en Azure-nätverkskort kopplat till en virtuell dator ändringar och IP-adress inom VM-operativsystem som är annorlunda kan du förlora anslutningen till den virtuella datorn.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Om jag avbryter en Cloud Service-distributionsplats eller stänga av en virtuell dator från inom operativsystemet, vad händer med Mina IP-adresser?
Inget. IP-adresser (offentlig VIP, offentliga och privata) fortsätter vara tilldelade cloud service-distributionsplats eller virtuell dator.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Kan jag flytta virtuella datorer från ett undernät till ett annat undernät i ett virtuellt nätverk utan att omdistribuera?
Ja. Du hittar mer information finns i den [hur du flyttar en virtuell dator eller roll-instans till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md) artikeln.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan jag konfigurera en statisk MAC-adress för min virtuella dator?
Nej. En MAC-adress kan inte konfigureras statiskt.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>MAC-adressen förblir densamma för den virtuella datorn när den har skapats?
Ja, MAC-adressen förblir densamma för en virtuell dator distribueras via både Resource Manager och klassiska distributionsmodeller tills det tas bort. MAC-adressen gavs tidigare, om den virtuella datorn har Stoppad (frigjord), men nu MAC-adressen bevaras även om den virtuella datorn är i frigjort tillstånd.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan jag ansluta till internet från en virtuell dator i ett virtuellt nätverk?
Ja. Alla virtuella datorer och molntjänster rollinstanser som distribuerats i ett virtuellt nätverk kan ansluta till Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-tjänster som ansluter till virtuella nätverk

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan jag använda Azure App Service Web Apps med ett virtuellt nätverk?
Ja. Du kan distribuera webbprogram i ett virtuellt nätverk med hjälp av en ASE (App Service Environment). Om du har en punkt-till-plats-anslutning som konfigurerats för ditt virtuella nätverk, kan alla webbprogram på ett säkert sätt ansluta och komma åt resurser i det virtuella nätverket. Mer information finns i följande artiklar:

* [Skapa Webbappar i App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Med hjälp av VNet-Integration och Hybridanslutningar med Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan jag distribuera Cloud Services med webb- och arbetsroller (PaaS) i ett virtuellt nätverk?
Ja. (Valfritt) kan du distribuera Cloud Services-rollinstanser i virtuella nätverk. Om du vill göra det anger du virtuella nätverkets namn och rollen/undernät-mappningar i konfigurationsavsnittet nätverk av tjänstens konfiguration. Du behöver inte uppdatera alla binära filer.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Kan jag ansluta en virtuell dator skala ange (VMSS) till ett virtuellt nätverk?
Ja. En VMSS måste du ansluta till ett virtuellt nätverk.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Finns det en fullständig lista över Azure-tjänster som kan jag distribuera resurser från i ett virtuellt nätverk?
Ja, mer information finns i [integrering av virtuella nätverk för Azure-tjänster](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Vilka Azure PaaS-resurser kan jag begränsa åtkomst till från ett virtuellt nätverk?

Resurser som distribueras via vissa Azure PaaS-tjänster (till exempel Azure Storage och Azure SQL Database), kan begränsa nätverksåtkomst till endast resurser i ett virtuellt nätverk genom att använda tjänstslutpunkter i virtuella nätverk. Mer information finns i [virtuella nätverk översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan jag flytta Mina tjänster och från virtuella nätverk?
Nej. Du kan inte flytta tjänster och från virtuella nätverk. Om du vill flytta en resurs till ett annat VNet, måste du ta bort och distribuera om resursen.

## <a name="security"></a>Säkerhet

### <a name="what-is-the-security-model-for-vnets"></a>Vad är säkerhetsmodell för virtuella nätverk?
Virtuella nätverk är isolerade från varandra och andra tjänster i Azure-infrastrukturen. Ett virtuellt nätverk är en förtroendegräns.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan jag begränsa inkommande eller utgående trafikflödet till VNet-anslutna resurser?
Ja. Du kan använda [Nätverkssäkerhetsgrupper](security-overview.md) enskilda undernät inom ett virtuellt nätverk, nätverkskort ansluten till ett virtuellt nätverk, eller båda.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan jag för att implementera en brandvägg mellan VNet-anslutna resurser?
Ja. Du kan distribuera en [virtuell nätverksinstallation för brandvägg](https://azure.microsoft.com/marketplace/?term=firewall) från flera leverantörer via Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Finns det information om hur du skyddar virtuella nätverk?
Ja. Mer information finns i [översikt över nätverkssäkerhet för Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API: er, scheman och verktyg

### <a name="can-i-manage-vnets-from-code"></a>Kan jag hantera virtuella nätverk från kod?
Ja. Du kan använda REST API: er för virtuella nätverk i den [Azure Resource Manager](/rest/api/virtual-network) och [klassisk (Service Management)](http://go.microsoft.com/fwlink/?LinkId=296833) distributionsmodeller.

### <a name="is-there-tooling-support-for-vnets"></a>Finns det verktygsstöd för virtuella nätverk?
Ja. Lär dig mer om hur du använder:
- Azure portal för att distribuera virtuella nätverk via den [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) och [klassiska](virtual-networks-create-vnet-classic-pportal.md) distributionsmodeller.
- PowerShell för att hantera virtuella nätverk som distribueras via den [Resource Manager](/powershell/module/azurerm.network) och [klassiska](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) distributionsmodeller.
- Azures kommandoradsgränssnitt (CLI) för att distribuera och hantera virtuella nätverk som distribueras via den [Resource Manager](/cli/azure/network/vnet) och [klassiska](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) distributionsmodeller.  

## <a name="vnet-peering"></a>VNET-peering

### <a name="what-is-vnet-peering"></a>Vad är VNet-peering?
VNet-peering (eller virtuell nätverkspeering) kan du ansluta virtuella nätverk. En VNet peering-anslutning mellan virtuella nätverk kan du dirigera trafik mellan dem privat via IPv4-adresser. Virtuella datorer i peerkopplade virtuella nätverk kan kommunicera med varandra som om de är i samma nätverk. Dessa virtuella nätverk kan vara i samma region eller i olika regioner (även kallat Global VNet-Peering). Också kan skapa VNet peering-anslutningar mellan Azure-prenumerationer.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Kan jag skapa en peering-anslutning till ett virtuellt nätverk i en annan region?
Ja. Global VNet-peering kan du peerkoppla virtuella nätverk i olika regioner. Global VNet-peering är tillgängligt i alla offentliga Azure-regioner. Du kan inte globalt peer-kopplas från offentliga Azure-regioner till nationella moln. Global peering är inte för närvarande tillgängligt i nationella moln.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kan jag aktivera VNet-Peering om min virtuella nätverk som hör till prenumerationer i olika Azure Active Directory-klienter?
Det är för närvarande inte möjligt att upprätta VNet-Peering (antingen lokal eller global) om dina prenumerationer tillhör olika Azure Active Directory-klienter.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Mina VNet-peering-anslutningen finns i *initierad* tillstånd, varför inte kan jag ansluta?
Om peering-anslutningen är i tillståndet initierad, innebär det du har skapat endast en länk. En dubbelriktad länk måste skapas för att upprätta en anslutning har. Till exempel to-peer VNet A VNet B, måste en länk skapas från VNetA till VNetB och VNetB till VNetA. Skapa båda länkarna ändrar status till *ansluten.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mina VNet-peering-anslutningen finns i *frånkopplad* tillstånd, varför inte kan jag skapa en peering-anslutningen?
Om VNet peering-anslutningen är i frånkopplat läge, innebär det att en av länkarna skapade har tagits bort. För att återupprätta en peeranslutning, behöver du ta bort länken och återskapa.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Kan jag peer mitt virtuella nätverk med ett virtuellt nätverk i en annan prenumeration?
Ja. Peerkoppling kan upprättas mellan virtuella nätverk mellan prenumerationer och mellan regioner.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Kan jag peerkoppla två virtuella nätverk med matchande eller överlappande adressintervall?
Nej. Adressutrymmen måste inte overalap om du vill aktivera VNet-Peering.

### <a name="how-much-do-vnet-peering-links-cost"></a>Hur mycket gör VNet-peering länkar kostnad?
Det är kostnadsfritt för att skapa en VNet-peering-anslutningen. Dataöverföringar för peering-anslutningar debiteras. [Se här](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Krypteras VNet-peering trafik?
Nej. Trafik mellan resurser i peerkopplade virtuella nätverk är privata och isolerade. Den är helt på Microsoft Backbone.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Varför är min peering-anslutningen i frånkopplat läge?
VNet-peering-anslutningar går du till *frånkopplad* tillstånd när en VNet-peeringlänk tas bort. Du måste ta bort båda länkarna för att återupprätta en lyckad peering-anslutningen.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Om jag peer-VNetA till VNetB och jag peer-VNetB till VNetC, betyder det VNetA och VNetC är peer-kopplade?
Nej. Transitiva peering stöds inte. Du måste peer VNetA och VNetC för den här för att äga rum.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Finns det några begränsningar i nätverksbandbredd för peering-anslutningar?
Nej. VNet-peering, lokal eller global, medför inte några bandbreddsbegränsningar. Bandbredd är endast limites av virtuell dator eller beräkning resursen.

