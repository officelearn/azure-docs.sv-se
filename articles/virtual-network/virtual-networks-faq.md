---
title: Vanliga frågor och svar om Virtuella Azure-nätverk
titlesuffix: Azure Virtual Network
description: Svar på de vanligaste frågorna om virtuella Microsoft Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: e45d5393833973889b28a95ec86b89593a091f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244815"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ) om Azure Virtual Network

## <a name="virtual-network-basics"></a>Grundläggande om virtuellt nätverk

### <a name="what-is-an-azure-virtual-network-vnet"></a>Vad är ett virtuellt Azure-nätverk (VNet)?
Ett Virtuellt Azure-nätverk (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan använda virtuella nätverk för att etablera och hantera virtuella privata nätverk (VPN) i Azure och eventuellt länka virtuella nätverk med andra virtuella nätverk i Azure eller med din lokala IT-infrastruktur för att skapa hybrid- eller lokala lösningar. Varje virtuella nätverk som du skapar har ett eget CIDR-block och kan länkas till andra virtuella nätverk och lokala nätverk så länge CIDR-blocken inte överlappar varandra. Du har också kontroll över DNS-serverinställningar för virtuella nätverk och segmentering av det virtuella nätverket i undernät.

Använd virtuella nätverk för att:

* Skapa ett dedikerat privat molnbaserat virtuella nätverk. Ibland behöver du inte en konfiguration över flera platser för din lösning. När du skapar ett virtuella nätverk kan dina tjänster och virtuella datorer i ditt virtuella nätverk kommunicera direkt och säkert med varandra i molnet. Du kan fortfarande konfigurera slutpunktsanslutningar för de virtuella datorer och tjänster som kräver Internet-kommunikation, som en del av din lösning.

* Utöka ditt datacenter på ett säkert sätt. Med virtuella nätverk kan du skapa traditionella S2S-VPN-vpn (Site-to-site) för att skala din datacenterkapacitet på ett säkert sätt. S2S VPN använder IPSEC för att tillhandahålla en säker anslutning mellan företagets VPN-gateway och Azure.

* Aktivera hybridmolnscenarier. Virtuella nätverk ger dig flexibiliteten att stödja en rad hybridmolnscenarier. Du kan säkert ansluta molnbaserade program till alla typer av lokala system som stordatorer och Unix-system.

### <a name="how-do-i-get-started"></a>Hur kommer jag igång?
Gå till [dokumentationen](https://docs.microsoft.com/azure/virtual-network/) för virtuella nätverk för att komma igång. Det här innehållet innehåller översikts- och distributionsinformation för alla virtuella nätverksfunktioner.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan jag använda virtuella nätverk utan anslutning över gränserna?
Ja. Du kan använda ett virtuella nätverk utan att ansluta det till din lokal. Du kan till exempel köra Microsoft Windows Server Active Directory-domänkontrollanter och SharePoint-grupper endast i ett Azure VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan jag utföra WAN-optimering mellan virtuella nätverk eller ett virtuella nätverk och mitt lokala datacenter?
Ja. Du kan distribuera en [virtuell WAN-optimeringsnätverksinstallation](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) från flera leverantörer via Azure Marketplace.

## <a name="configuration"></a>Konfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Vilka verktyg använder jag för att skapa ett virtuella nätverk?
Du kan använda följande verktyg för att skapa eller konfigurera ett virtuella nätverk:

* Azure Portal
* PowerShell
* Azure CLI
* En nätverkskonfigurationsfil (netcfg - endast för klassiska virtuella nätverk). Se [konfigurera ett virtuella nätverk med hjälp av en fil artikel i nätverkskonfiguration.](virtual-networks-using-network-configuration-file.md)

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Vilka adressintervall kan jag använda i mina virtuella nätverk?
Alla IP-adressintervall som definieras i [RFC 1918](https://tools.ietf.org/html/rfc1918). Till exempel 10.0.0.0/16. Du kan inte lägga till följande adressintervall:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Sändning)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Länk-lokal)
* 168.63.129.16/32 (Intern DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan jag ha offentliga IP-adresser i mina virtuella nätverk?
Ja. Mer information om offentliga IP-adressintervall finns i [Skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network). Offentliga IP-adresser är inte direkt tillgängliga från Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Finns det en gräns för antalet undernät i mitt virtuella nätverk?
Ja. Mer information finns i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Undernätsadressutrymmen kan inte överlappa varandra.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Finns det några begränsningar för att använda IP-adresser i dessa undernät?
Ja. Azure reserverar 5 IP-adresser i varje undernät. These are x.x.x.0-x.x.x.3 and the last address of the subnet. x.x.x.1-x.x.x.3 är reserverad i varje undernät för Azure-tjänster.   
- x.x.x.0: Nätverksadress
- x.x.x.1: Reserverad av Azure för standardgatewayen
- x.x.x.2, x.x.x.3: Reserverad av Azure för att mappa Azure DNS-IPs till VNet-utrymmet
- x.x.x.255: Nätverkssändningsadress

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hur små och hur stora kan virtuella nätverk och undernät vara?
Det minsta IPv4-undernätet som stöds är /29 och det största är /8 (med CIDR-undernätsdefinitioner).  IPv6-undernät måste vara exakt /64 i storlek.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan jag ta med mina VIRTUELLA nätverk till Azure med virtuella nätverk?
Nej. Virtuella nätverk är Layer-3-överlägg. Azure stöder inte någon Layer-2-semantik.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan jag ange anpassade routningsprinciper för mina virtuella nätverk och undernät?
Ja. Du kan skapa en flödestabell och associera den med ett undernät. Mer information om routning i Azure finns i [Översikt över Routning](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Stöder VNets multicast eller broadcast?
Nej. Multicast och broadcast stöds inte.

### <a name="what-protocols-can-i-use-within-vnets"></a>Vilka protokoll kan jag använda inom virtuella nätverk?
Du kan använda TCP-, UDP- och ICMP TCP/IP-protokoll i virtuella nätverk. Unicast stöds inom virtuella nätverk, med undantag för DHCP (Dynamic Host Configuration Protocol) via Unicast (källasport UDP/68 / målport UDP/67). Multicast-, broadcast-, IP-in-IP-inkapslade paket och GRE-paket (Generic Routing Inkapsling) blockeras i virtuella nätverk. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan jag pinga mina standardroutrar i ett virtuella nätverk?
Nej.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan jag använda tracert för att diagnostisera anslutning?
Nej.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan jag lägga till undernät när det virtuella nätverket har skapats?
Ja. Undernät kan läggas till i virtuella nätverk när som helst så länge undernätsadressintervallet inte är en del av ett annat undernät och det finns tillgängligt utrymme kvar i det virtuella nätverkets adressintervall.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan jag ändra storleken på mitt undernät när jag har skapat det?
Ja. Du kan lägga till, ta bort, expandera eller krympa ett undernät om inga virtuella datorer eller tjänster är distribuerade i det.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Kan jag ändra undernät när jag har skapat dem?
Ja. Du kan lägga till, ta bort och ändra CIDR-block som används av ett virtuella nätverk.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Om jag kör mina tjänster i ett virtuella nätverk, kan jag ansluta till internet?
Ja. Alla tjänster som distribueras inom ett virtuella nätverk kan ansluta utgående till internet. Mer information om utgående internetanslutningar i Azure finns i [Utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill ansluta inkommande till en resurs som distribueras via Resource Manager måste resursen ha tilldelats en offentlig IP-adress. Mer information om offentliga IP-adresser finns i [Offentliga IP-adresser](virtual-network-public-ip-address.md). Varje Azure Cloud Service som distribueras i Azure har en offentligt adresserbar VIP som tilldelats den. Du definierar indataslutpunkter för PaaS-roller och slutpunkter för virtuella datorer för att göra det möjligt för dessa tjänster att acceptera anslutningar från Internet.

### <a name="do-vnets-support-ipv6"></a>Har VNets stöd för IPv6?
Ja, virtuella nätverk kan vara IPv4-eller dual stack (IPv4+IPv6).  Mer information finns i [Översikt över IPv6 för Virtuella Azure-nätverk](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Kan en VNet-span regioner?
Nej. Ett virtuella nätverk är begränsat till en enda region. Ett virtuellt nätverk sträcker sig dock över tillgänglighetszoner. Mer information om tillgänglighetszoner finns i [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Du kan ansluta virtuella nätverk i olika regioner med virtuell nätverks peering. Mer information finns i [Översikt över virtuell nätverks peering](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan jag ansluta ett virtuella nätverk till ett annat virtuella nätverk i Azure?
Ja. Du kan ansluta ett virtuella nätverk till ett annat virtuella nätverk med hjälp av antingen:
- **Virtuell nätverks peering:** Mer information finns i [VNet-peering översikt](virtual-network-peering-overview.md)
- **En Azure VPN-gateway:** Mer information finns i [Konfigurera en VNet-till-VNet-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Namnmatchning (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Vilka är mina DNS-alternativ för virtuella nätverk?
Använd beslutstabellen på sidan [Namnmatchning för virtuella datorer och rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md) för att guida dig genom alla tillgängliga DNS-alternativ.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan jag ange DNS-servrar för ett virtuella nätverk?
Ja. Du kan ange IP-adresser för DNS-servern i VNet-inställningarna. Inställningen används som standard-DNS-server för alla virtuella datorer i det virtuella nätverket.

### <a name="how-many-dns-servers-can-i-specify"></a>Hur många DNS-servrar kan jag ange?
Referens [Azure gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan jag ändra mina DNS-servrar när jag har skapat nätverket?
Ja. Du kan när som helst ändra DNS-serverlistan för ditt virtuella nätverk. Om du ändrar DNS-serverlistan måste du utföra en DHCP-lånsyror på alla berörda virtuella datorer i det virtuella nätverket för att de nya DNS-inställningarna ska börja gälla. För virtuella datorer som kör Windows OS `ipconfig /renew` kan du göra detta genom att skriva direkt på den virtuella datorn. För andra OS-typer finns i DHCP-låneförnyelsen för den specifika OS-typen. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Vad är DNS som tillhandahålls av Azure och fungerar det med virtuella nätverk?
Azure-provided DNS är en DNS-tjänst med flera innehavare som erbjuds av Microsoft. Azure registrerar alla dina virtuella datorer och molntjänstrollinstanser i den här tjänsten. Den här tjänsten tillhandahåller namnmatchning efter värdnamn för virtuella datorer och rollinstanser som finns i samma molntjänst och av FQDN för virtuella datorer och rollinstanser i samma virtuella nätverk. Mer information om DNS finns i [Namnmatchning för virtuella datorer och rollinstanser i Molntjänster](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Det finns en begränsning till de första 100 molntjänsterna i ett VNet för namnmatchning mellan innehavare med Azure-erhållen DNS. Om du använder din egen DNS-server gäller inte den här begränsningen.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Kan jag åsidosätta mina DNS-inställningar per virtuell dator eller molntjänst?
Ja. Du kan ange att DNS-servrar per virtuell dator eller molntjänst ska åsidosätta standardnätverksinställningarna. Vi rekommenderar dock att du använder dns i hela nätverket så mycket som möjligt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan jag ta med mitt eget DNS-suffix?
Nej. Du kan inte ange ett anpassat DNS-suffix för dina virtuella nätverk.

## <a name="connecting-virtual-machines"></a>Ansluta virtuella datorer

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan jag distribuera virtuella datorer till ett virtuella nätverk?
Ja. Alla nätverksgränssnitt (NIC) som är anslutna till en virtuell dator som distribueras via resurshanterarens distributionsmodell måste vara anslutna till ett virtuellt nätverk. Virtuella datorer som distribueras via den klassiska distributionsmodellen kan eventuellt anslutas till ett virtuella nätverk.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Vilka olika typer av IP-adresser kan jag tilldela till virtuella datorer?
* **Privat:** Tilldelas till varje nätverkskort inom varje virtuell dator. Adressen tilldelas med hjälp av antingen statisk eller dynamisk metod. Privata IP-adresser tilldelas från det intervall som du angav i inställningarna för undernätet i ditt virtuella nätverk. Resurser som distribueras via den klassiska distributionsmodellen tilldelas privata IP-adresser, även om de inte är anslutna till ett virtuella nätverk. Allokeringsmetodens beteende är olika beroende på om en resurs har distribuerats med Resurshanteraren eller den klassiska distributionsmodellen: 

  - **Resurshanteraren**: En privat IP-adress som tilldelats med den dynamiska eller statiska metoden förblir tilldelad en virtuell dator (Resource Manager) tills resursen tas bort. Skillnaden är att du väljer den adress som ska tilldelas när du använder statisk, och Azure väljer när du använder dynamisk. 
  - **Klassisk:** En privat IP-adress som tilldelats med den dynamiska metoden kan ändras när en virtuell dator (klassisk) virtuell dator startas om efter att ha stoppats (hanterat) tillstånd. Om du behöver se till att den privata IP-adressen för en resurs som distribueras via den klassiska distributionsmodellen aldrig ändras, tilldelar du en privat IP-adress med den statiska metoden.

* **Allmänheten:** Du kan också tilldela nätverkskort som är kopplade till virtuella datorer som distribueras via distributionsmodellen för Azure Resource Manager. Adressen kan tilldelas med den statiska eller dynamiska allokeringsmetoden. Alla virtuella datorer och molntjänstrollinstanser som distribueras via den klassiska distributionsmodellen finns i en molntjänst, som tilldelas en *dynamisk,* offentlig virtuell IP-adress (VIP). En offentlig *statisk* IP-adress, som kallas [en reserverad IP-adress,](virtual-networks-reserved-public-ip.md)kan eventuellt tilldelas som en VIP. Du kan tilldela offentliga IP-adresser till enskilda virtuella datorer eller molntjänstrollinstanser som distribueras via den klassiska distributionsmodellen. Dessa adresser kallas [offentlig IP-ip -ip -ip -ip -adress på instansnivå (ILPIP-adresser](virtual-networks-instance-level-public-ip.md) och kan tilldelas dynamiskt.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan jag reservera en privat IP-adress för en virtuell dator som jag skapar vid ett senare tillfälle?
Nej. Du kan inte reservera en privat IP-adress. Om en privat IP-adress är tillgänglig tilldelas den till en virtuell dator eller rollinstans av DHCP-servern. Den virtuella datorn kan vara den som du vill att den privata IP-adressen ska tilldelas. Du kan dock ändra den privata IP-adressen för en redan skapad virtuell dator till en tillgänglig privat IP-adress.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Ändras privata IP-adresser för virtuella datorer i ett virtuella nätverk?
Det beror på. Om den virtuella datorn har distribuerats via Resource Manager, nej, oavsett om IP-adressen har tilldelats med den statiska eller dynamiska allokeringsmetoden. Om den virtuella datorn har distribuerats via den klassiska distributionsmodellen kan dynamiska IP-adresser ändras när en virtuell dator startas efter att ha stoppats (deallocated) tillstånd. Adressen frigörs från en virtuell dator som distribueras via distributionsmodellen när den virtuella datorn tas bort.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan jag manuellt tilldela IP-adresser till nätverkskort i operativsystemet VM?
Ja, men det rekommenderas inte om det inte är nödvändigt, till exempel när du tilldelar flera IP-adresser till en virtuell dator. Mer information finns i [Lägga till flera IP-adresser på en virtuell dator](virtual-network-multiple-ip-addresses-portal.md#os-config). Om IP-adressen som tilldelats ett Azure-nätverkskort som är kopplat till ett virtuellt nätverksanslag ändras och IP-adressen i operativsystemet vm är annorlunda, förlorar du anslutningen till den virtuella datorn.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Vad händer med mina IP-adresser om jag stoppar en distributionsplats för molntjänsten eller stänger av en virtuell dator inifrån operativsystemet?
Ingenting. IP-adresserna (offentlig VIP, offentlig och privat) förblir tilldelade till molntjänstdistributionsplatsen eller den virtuella datorn.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Kan jag flytta virtuella datorer från ett undernät till ett annat undernät i ett virtuella nätverk utan att distribuera om?
Ja. Du hittar mer information i [hur du flyttar en virtuell dator eller rollinstans till en annan undernätsartikel.](virtual-networks-move-vm-role-to-subnet.md)

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan jag konfigurera en statisk MAC-adress för min virtuella dator?
Nej. Det går inte att konfigurera en MAC-adress statiskt.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Kommer MAC-adressen att vara densamma för min virtuella dator när den har skapats?
Ja, MAC-adressen förblir densamma för en virtuell dator som distribueras via både Resource Manager och klassiska distributionsmodeller tills den tas bort. Tidigare släpptes MAC-adressen om den virtuella datorn stoppades (deallocated), men nu behålls MAC-adressen även när den virtuella datorn är i deallocated-tillstånd. MAC-adressen förblir tilldelad nätverksgränssnittet tills nätverksgränssnittet tas bort eller den privata IP-adressen som tilldelats den primära IP-konfigurationen för det primära nätverksgränssnittet ändras. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan jag ansluta till internet från en virtuell dator i ett virtuellt nätverk?
Ja. Alla virtuella datorer och molntjänstrollinstanser som distribueras i ett virtuella nätverk kan ansluta till Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-tjänster som ansluter till virtuella nätverk

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan jag använda Azure App Service Web Apps med ett virtuella nätverk?
Ja. Du kan distribuera webbappar i ett virtuella nätverk med hjälp av ett ASE (App Service Environment), ansluta backend för dina appar till dina virtuella nätverk med VNet-integrering och låsa inkommande trafik till din app med tjänstslutpunkter. Mer information finns i följande artiklar:

* [Nätverksfunktioner för App Service](../app-service/networking-features.md)
* [Skapa webbappar i en apptjänstmiljö](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrera en app med Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Åtkomstbegränsningar för App Service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan jag distribuera molntjänster med webb- och arbetsroller (PaaS) i ett virtuella nätverk?
Ja. Du kan (eventuellt) distribuera molntjänstrollinstanser inom virtuella nätverk. För att göra det anger du VNet-namnet och roll-/undernätsmappningarna i nätverkskonfigurationsavsnittet i tjänstkonfigurationen. Du behöver inte uppdatera någon av dina binärfiler.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Kan jag ansluta en skala för virtuella datorer till ett virtuellt nätverk?
Ja. Du måste ansluta en skala för virtuella datorer till ett virtuellt nätverk.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Finns det en fullständig lista över Azure-tjänster som jag kan distribuera resurser från till ett virtuella nätverk?
Ja, mer information finns i [Integrering av virtuella nätverk för Azure-tjänster](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Hur kan jag begränsa åtkomsten till Azure PaaS-resurser från ett virtuella nätverk?

Resurser som distribueras via vissa Azure PaaS-tjänster (till exempel Azure Storage och Azure SQL Database) kan begränsa nätverksåtkomsten till VNet med hjälp av slutpunkter för virtuella nätverkstjänster eller Azure Private Link. Mer information finns i [översikt över slutpunkter för virtuella nätverkstjänster ,](virtual-network-service-endpoints-overview.md)översikt över Azure Private [Link](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan jag flytta mina tjänster till och från virtuella nätverk?
Nej. Du kan inte flytta tjänster till och från virtuella nätverk. Om du vill flytta en resurs till ett annat virtuella nätverk måste du ta bort och distribuera om resursen.

## <a name="security"></a>Säkerhet

### <a name="what-is-the-security-model-for-vnets"></a>Vad är säkerhetsmodellen för virtuella nätverk?
Virtuella nätverk är isolerade från varandra och andra tjänster som finns i Azure-infrastrukturen. Ett virtuella nätverk är en förtroendegräns.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan jag begränsa inkommande eller utgående trafikflöde till VNet-anslutna resurser?
Ja. Du kan använda [nätverkssäkerhetsgrupper](security-overview.md) på enskilda undernät i ett virtuella nätverk, nätverkskort som är anslutna till ett virtuella nätverk eller båda.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan jag implementera en brandvägg mellan virtuella nätverksanslutna resurser?
Ja. Du kan distribuera en [virtuell brandväggsnätverksinstallation](https://azure.microsoft.com/marketplace/?term=firewall) från flera leverantörer via Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Finns det information om hur du skyddar virtuella nätverk?
Ja. Mer information finns i [Översikt över Azure Network Security](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API:er, scheman och verktyg

### <a name="can-i-manage-vnets-from-code"></a>Kan jag hantera virtuella nätverk från kod?
Ja. Du kan använda REST API:er för virtuella nätverk i [Azure Resource Manager](/rest/api/virtual-network) och [klassiska](https://go.microsoft.com/fwlink/?LinkId=296833) distributionsmodeller.

### <a name="is-there-tooling-support-for-vnets"></a>Finns det verktygsstöd för virtuella nätverk?
Ja. Läs mer om hur du använder:
- Azure-portalen för att distribuera virtuella nätverk via [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) och [klassiska](virtual-networks-create-vnet-classic-pportal.md) distributionsmodeller.
- PowerShell för att hantera virtuella nätverk som distribueras via [Resource Manager](/powershell/module/az.network) och [klassiska](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) distributionsmodeller.
- Azure-kommandoradsgränssnittet (CLI) för att distribuera och hantera virtuella nätverk som distribueras via [Resource Manager](/cli/azure/network/vnet) och [klassiska](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) distributionsmodeller.  

## <a name="vnet-peering"></a>VNet-peering

### <a name="what-is-vnet-peering"></a>Vad är VNet-peering?
Med VNet-peering (eller virtuell nätverks peering) kan du ansluta virtuella nätverk. Med en VNet-peering-anslutning mellan virtuella nätverk kan du dirigera trafik mellan dem privat via IPv4-adresser. Virtuella datorer i peer-virtuella nätverk kan kommunicera med varandra som om de finns i samma nätverk. Dessa virtuella nätverk kan finnas i samma region eller i olika regioner (kallas även Global VNet Peering). VNet-peering-anslutningar kan också skapas över Azure-prenumerationer.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Kan jag skapa en peering-anslutning till ett VNet i en annan region?
Ja. Med global VNet-peering kan du peer-virtuella nätverk i olika regioner. Global VNet-peering är tillgänglig i alla offentliga Azure-regioner, Kinamolnregioner och molnregioner för myndigheter. Du kan inte peeras globalt från offentliga Azure-regioner till nationella molnregioner.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Vilka är begränsningarna relaterade till Global VNet Peering och Load Balancers?
Om de två virtuella nätverken i två olika regioner är peered över Global VNet Peering, kan du inte ansluta till resurser som ligger bakom en grundläggande belastningsutjämnare via frontend IP för lastjämkaren. Den här begränsningen finns inte för en standardbelastningsutjämnare.
Följande resurser kan använda grundläggande belastningsutjämnare, vilket innebär att du inte kan nå dem via belastningsutjämnarens Front End IP över Global VNet Peering. Du kan dock använda Global VNet-peering för att nå resurserna direkt via deras privata VIRTUELLA Nätverk-IPs, om det är tillåtet. 
- Virtuella datorer bakom grundläggande belastningsutjämnare
- Skalsatser för virtuella datorer med grundläggande belastningsutjämnare 
- Redis Cache 
- Program gateway (v1) SKU
- Service Fabric
- SQL-mi
- API Management
- Active Directory Domain Service (ADD)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service-miljön

Du kan ansluta till dessa resurser via ExpressRoute eller VNet-till-VNet via VNet Gateways.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kan jag aktivera VNet-peering om mina virtuella nätverk tillhör prenumerationer inom olika Azure Active Directory-klienter?
Ja. Det är möjligt att upprätta VNet Peering (oavsett om det är lokalt eller globalt) om dina prenumerationer tillhör olika Azure Active Directory-klienter. Du kan göra detta via PowerShell eller CLI. Portalen stöds ännu inte.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Min VNet-peering-anslutning är i *initierat* tillstånd, varför kan jag inte ansluta?
Om peering-anslutningen är i ett *initierat* tillstånd innebär det att du bara har skapat en länk. En dubbelriktad länk måste skapas för att upprätta en lyckad anslutning. Till exempel för att peer VNet A till VNet B, en länk måste skapas från VNetA till VNetB och från VNetB till VNetA. Om du skapar båda länkarna ändras läget till *Ansluten*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Min VNet-peering-anslutning är i *frånkopplat* tillstånd, varför kan jag inte skapa en peering-anslutning?
Om din VNet-peering-anslutning är i ett *frånkopplat* tillstånd betyder det att en av länkarna som skapats har tagits bort. För att återupprätta en peering-anslutning måste du ta bort länken och återskapa den.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Kan jag peer my VNet med ett VNet i en annan prenumeration?
Ja. Du kan peer-nätverk mellan prenumerationer och mellan regioner.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Kan jag peer två virtuella nätverk med matchande eller överlappande adressintervall?
Nej. Adressutrymmen får inte överlappa för att aktivera VNet-peering.

### <a name="how-much-do-vnet-peering-links-cost"></a>Hur mycket kostar VNet-peering-länkar?
Det finns ingen avgift för att skapa en VNet-peering-anslutning. Dataöverföring över peering-anslutningar debiteras. [Se här](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Är VNet-peering-trafik krypterad?
Nej. Trafiken mellan resurser i peer-peer-virtuella nätverk är privat och isolerad. Det är fortfarande helt på Microsoft Backbone.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Varför är min peering-anslutning *frånkopplad?*
VNet-peering-anslutningar går till *frånkopplat* tillstånd när en VNet-peering-länk tas bort. Du måste ta bort båda länkarna för att återupprätta en lyckad peering-anslutning.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Om jag peer VNetA till VNetB och jag peer VNetB till VNetC, betyder det VNetA och VNetC är peered?
Nej. Transitiv peering stöds inte. Du måste peer VNetA och VNetC för att detta ska ske.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Finns det några bandbreddsbegränsningar för peering-anslutningar?
Nej. VNet-peering, oavsett om det är lokalt eller globalt, medför inga bandbreddsbegränsningar. Bandbredden begränsas endast av den virtuella datorn eller beräkningsresursen.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Hur felsöker jag problem med VNet-peering?
Här är en [felsökare guide](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) du kan prova.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Vilka Azure-regioner är tillgängliga för virtuellt nätverk TAP?
Förhandsgranskning av virtuellt nätverk TAP är tillgänglig i alla Azure-regioner. De övervakade nätverksgränssnitten, TAP-resursen för det virtuella nätverket och samlare eller analyslösningen måste distribueras i samma region.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Stöder Virtual Network TAP alla filtreringsfunktioner på de speglade paketen?
Filtreringsfunktioner stöds inte med den virtuella tap-förhandsgranskningen av nätverket. När en TAP-konfiguration läggs till i ett nätverksgränssnitt strömmas en djup kopia av all in- och utgående trafik i nätverksgränssnittet till TAP-målet.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Kan flera TAP-konfigurationer läggas till i ett övervakat nätverksgränssnitt?
Ett övervakat nätverksgränssnitt kan bara ha en TAP-konfiguration. Kontrollera med den enskilda [partnerlösningen](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) om du vill strömma flera kopior av TAP-trafiken till de analysverktyg du väljer.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Kan samma virtuella nätverk TAP-resurs aggregera trafik från övervakade nätverksgränssnitt i mer än ett virtuellt nätverk?
Ja. Samma virtuella TAP-resurs för det virtuella nätverket kan användas för att aggregera speglad trafik från övervakade nätverksgränssnitt i peered virtuella nätverk i samma prenumeration eller en annan prenumeration. TAP-resursen för det virtuella nätverket och målbelastningsutjämnaren eller målnätverksgränssnittet måste finnas i samma prenumeration. Alla prenumerationer måste finnas under samma Azure Active Directory-klientorganisation.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Finns det några prestandaöverväganden på produktionstrafik om jag aktiverar en virtuell TAP-konfiguration i ett nätverk i ett nätverksgränssnitt?

Virtuellt nätverk TAP är i förhandsgranskning. Under förhandsversionen finns det inget servicenivåavtal. Funktionen bör inte användas för produktionsarbetsbelastningar. När ett nätverksgränssnitt för virtuella datorer är aktiverat med en TAP-konfiguration används samma resurser på Azure-värden som allokerats till den virtuella datorn för att skicka produktionstrafiken för att utföra speglingsfunktionen och skicka speglade paket. Välj rätt storlek på [virtuella Linux-](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows-datorer](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att säkerställa att det finns tillräckliga resurser för att den virtuella datorn ska kunna skicka produktionstrafiken och den speglade trafiken.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Stöds accelererade nätverk för [Linux](create-vm-accelerated-networking-cli.md) eller [Windows](create-vm-accelerated-networking-powershell.md) med virtuellt nätverk TAP?

Du kan lägga till en TAP-konfiguration i ett nätverksgränssnitt som är kopplat till en virtuell dator som är aktiverad med accelererade nätverk. Men prestanda och svarstid på den virtuella datorn påverkas genom att lägga till TAP-konfiguration eftersom avlastningen för spegling av trafik för närvarande inte stöds av Azure-accelererade nätverk.

## <a name="virtual-network-service-endpoints"></a>Tjänstslutpunkter för virtuellt nätverk

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Vad är rätt sekvens av åtgärder för att ställa in tjänstslutpunkter till en Azure-tjänst?
Det finns två steg för att skydda en Azure-tjänstresurs via tjänstslutpunkter:
1. Aktivera tjänstslutpunkter för Azure-tjänsten.
2. Konfigurera VNet-ACL:er för Azure-tjänsten.

Det första steget är en nätverksåtgärd och det andra steget är en tjänstresursresursåtgärd. Båda stegen kan utföras antingen av samma administratör eller olika administratörer baserat på RBAC-behörigheter som tilldelats administratörsrollen. Vi rekommenderar att du först aktiverar tjänstslutpunkter för ditt virtuella nätverk innan du konfigurerar VNet-ACL:er på Azure-tjänstsidan. Därför måste stegen utföras i sekvensen ovan för att ställa in VNet-tjänstslutpunkter.

>[!NOTE]
> Båda åtgärderna som beskrivs ovan måste slutföras innan du kan begränsa Azure-tjänstens åtkomst till det tillåtna virtuella nätverket och undernätet. Att bara aktivera tjänstslutpunkter för Azure-tjänsten på nätverkssidan ger dig inte den begränsade åtkomsten. Dessutom måste du också ställa in VNet-ACL:er på Azure-tjänstsidan.

Vissa tjänster (till exempel SQL och CosmosDB) tillåter undantag från ovanstående sekvens via flaggan **IgnoreMissingVnetServiceEndpoint.** När flaggan är inställd på **True**kan VNet-ACL:er ställas in på Azure-tjänstsidan innan du konfigurerar tjänstslutpunkterna på nätverkssidan. Azure-tjänster tillhandahåller den här flaggan för att hjälpa kunder i fall där de specifika IP-brandväggarna är konfigurerade på Azure-tjänster och aktivera tjänstslutpunkterna på nätverkssidan kan leda till en anslutningsavbrott eftersom käll-IP-adressen ändras från en offentlig IPv4-adress till en privat adress. Ställa in VNet-ACL:er på Azure-tjänstsidan innan du ställer in tjänstslutpunkter på nätverkssidan kan du undvika en anslutningsavbrott.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Finns alla Azure-tjänster i det virtuella Azure-nätverket som tillhandahålls av kunden? Hur fungerar slutpunkten för VNet-tjänst med Azure-tjänster?

Nej, alla Azure-tjänster finns inte i kundens virtuella nätverk. Majoriteten av Azure-datatjänster som Azure Storage, Azure SQL och Azure Cosmos DB är tjänster med flera innehavare som kan nås via offentliga IP-adresser. Du kan läsa mer om integrering av virtuella nätverk för Azure-tjänster [här](virtual-network-for-azure-services.md). 

När du använder slutpunktsfunktionen för VNet-tjänsten (aktivera VNet-tjänstslutpunkten på nätverkssidan och konfigurera lämpliga VNet-ACL:er på Azure-tjänstsidan) begränsas åtkomsten till en Azure-tjänst från ett tillåtet virtuella nätverk och undernät.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Hur ger slutpunkten för VNet-tjänsten säkerhet?

Slutpunktsfunktionen för VNet-tjänst (aktivera VNet-tjänstslutpunkten på nätverkssidan och konfigurera lämpliga VNet-ACL:er på Azure-tjänstsidan) begränsar Azure-tjänstens åtkomst till det tillåtna virtuella nätverket och undernätet, vilket ger en säkerhet på nätverksnivå och isolering av Azure-tjänsttrafiken. All trafik som använder VNet-tjänstslutpunkter flödar över Microsofts stamnät, vilket ger ytterligare ett lager av isolering från det offentliga internet. Dessutom kan kunderna välja att helt ta bort offentlig Internet-åtkomst till Azure-tjänstresurserna och endast tillåta trafik från deras virtuella nätverk genom en kombination av IP-brandvägg och VNet-ACL: er, vilket skyddar Azure-tjänstresurserna från obehöriga Tillgång.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Vad skyddar slutpunkten för VNet-tjänsten – VNet-resurser eller Azure-tjänst?
Slutpunkter för VNet-tjänst hjälper till att skydda Azure-tjänstresurser. VNet-resurser skyddas via NSG(Network Security Groups).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Finns det någon kostnad för att använda VNet-tjänstslutpunkter?

Nej, det finns ingen extra kostnad för att använda slutpunkter för VNet-tjänsten.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Kan jag aktivera slutpunkter för VNet-tjänsten och konfigurera VNet-ACL:er om det virtuella nätverket och Azure-tjänstresurserna tillhör olika prenumerationer?

Ja, det kan du. Virtuella nätverk och Azure-tjänstresurser kan vara antingen i samma eller olika prenumerationer. Det enda kravet är att både det virtuella nätverket och Azure-tjänstresurserna måste vara under samma Active Directory (AD) klientorganisation.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Kan jag aktivera slutpunkter för VNet-tjänsten och konfigurera VNet-ACL:er om det virtuella nätverket och Azure-tjänstresurserna tillhör olika AD-klienter?
Nej, slutpunkter för VNet-tjänsten och VNet-ACL stöds inte för AD-klienter.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Kan en lokal enhets IP-adress som är ansluten via Azure Virtual Network gateway (VPN) eller ExpressRoute gateway komma åt Azure PaaS-tjänsten via VNet-tjänstslutpunkter?
Som standard kan Azure-tjänstresurser som skyddas på virtuella nätverk inte nås från lokala nätverk. Om du vill tillåta trafik från lokala, måste du också tillåta offentliga (vanligtvis NAT) IP-adresser från din lokala eller ExpressRoute. Dessa IP-adresser kan läggas till via IP-brandväggskonfigurationen för Azure-tjänstresurserna.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Kan jag använda VNet Service Endpoint-funktionen för att skydda Azure-tjänsten till flera undernät i ett virtuellt nätverk eller i flera virtuella nätverk?
Om du vill skydda Azure-tjänster till flera undernät i ett virtuellt nätverk eller i flera virtuella nätverk aktiverar du tjänstslutpunkter på nätverkssidan på vart och ett av undernäten oberoende av varandra och skyddar sedan Azure-tjänstresurser till alla undernät genom att konfigurera lämpliga VNet-ACL:er på Azure-tjänstsidan.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Hur kan jag filtrera utgående trafik från ett virtuellt nätverk till Azure-tjänster och ändå använda tjänstslutpunkter?
Om du vill granska eller filtrera trafiken mot en Azure-tjänst från ett virtuellt nätverk kan du distribuera en virtuell nätverksinstallation inom det virtuella nätverket. Du kan sedan använda tjänstslutpunkter i undernätet där den virtuella nätverksinstallationen distribueras och säkra Azure-tjänstresurser endast till det här undernätet via VNet ACLs. Det här scenariot kan också vara användbart om du vill begränsa Azure-tjänståtkomst från ditt virtuella nätverk endast till specifika Azure-resurser med hjälp av nätverksfiltring av virtuella enheter. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Vad händer när du öppnar ett Azure-tjänstkonto som har en ACL (Virtual Network Access Control List) aktiverad utanför det virtuella nätverket?
HTTP 403- eller HTTP 404-felet returneras.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Tillåts undernät i ett virtuellt nätverk i olika regioner att komma åt ett Azure-tjänstkonto i en annan region? 
Ja, för de flesta Azure-tjänster kan virtuella nätverk som skapas i olika regioner komma åt Azure-tjänster i en annan region via slutpunkterna för VNet-tjänsten. Om ett Azure Cosmos DB-konto till exempel finns i västra USA eller östra USA och virtuella nätverk finns i flera regioner, kan det virtuella nätverket komma åt Azure Cosmos DB. Lagring och SQL är undantag och är regionala till sin natur och både det virtuella nätverket och Azure-tjänsten måste vara i samma region.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Kan en Azure-tjänst ha både en VNet ACL och en IP-brandvägg?
Ja, en VNet ACL och en IP-brandvägg kan samexistera. Båda funktionerna kompletterar varandra för att säkerställa isolering och säkerhet.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Vad händer om du tar bort ett virtuellt nätverk eller undernät som har tjänstslutpunkten aktiverat för Azure-tjänsten?
Borttagning av virtuella nätverk och undernät är oberoende åtgärder och stöds även när tjänstslutpunkter är aktiverade för Azure-tjänster. I de fall där Azure-tjänsterna har VNet-ACL:er konfigurerade, för dessa virtuella nätverk och undernät, tas VNet ACL-informationen som är associerad med den Azure-tjänsten inaktiverad när ett virtuella nätverk eller undernät som har VNet-tjänstslutpunkten aktiverat tas bort.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Vad händer om ett Azure-tjänstkonto som har en VNet-tjänstslutpunkt aktiverad tas bort?
Borttagningen av ett Azure-tjänstkonto är en oberoende åtgärd och stöds även när tjänstslutpunkten är aktiverad på nätverkssidan och VNet-ACL:er har konfigurerats på Azure-tjänstsidan. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Vad händer med käll-IP-adressen för en resurs (som en virtuell dator i ett undernät) som har VNet-tjänstslutpunkten aktiverat?
När slutpunkter för virtuella nätverkstjänster är aktiverade växlar käll-IP-adresserna för resurserna i det virtuella nätverkets undernät från att använda offentliga IPV4-adresser till Det virtuella Azure-nätverkets privata IP-adresser för trafik till Azure-tjänst. Observera att detta kan orsaka specifika IP-brandväggar som är inställda på offentlig IPV4-adress tidigare på Azure-tjänsterna misslyckas. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Har tjänstens slutpunktsflöde alltid företräde?
Tjänstslutpunkter lägger till en systemväg som har företräde framför BGP-vägar och ger optimal routning för tjänstens slutpunktstrafik. Tjänstslutpunkter tar alltid tjänsttrafik direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure-stamnätet. Mer information om hur Azure väljer en väg finns i [Azure Virtual Network Traffic routing](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Hur fungerar NSG på ett undernät med tjänstslutpunkter?
För att nå Azure-tjänsten måste NSG:er tillåta utgående anslutning. Om dina NSG:er öppnas för all utgående internettrafik ska tjänstens slutpunktstrafik fungera. Du kan också begränsa den utgående trafiken till tjänst-IPs endast med hjälp av tjänsttaggarna.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Vilka behörigheter behöver jag för att konfigurera tjänstslutpunkter?
Tjänstslutpunkter kan konfigureras i ett virtuellt nätverk oberoende av en användare med skrivåtkomst till det virtuella nätverket. För att skydda Azure-tjänstresurser till ett virtuellt nätverk måste användaren ha behörighet **microsoft.Network/virtualNetworks/undernät/joinViaServiceEndpoint/åtgärd** för de undernät som läggs till. Den här behörigheten ingår som standard i den inbyggda tjänstadministratörsrollen och kan ändras genom att skapa anpassade roller. Lär dig mer om inbyggda roller och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Kan jag filtrera virtuell nätverkstrafik till Azure-tjänster, vilket endast tillåter specifika azure-tjänstresurser, via slutpunkter för VNet-tjänsten? 

Med VNet-tjänstslutpunktsprinciper (Virtual Network) kan du filtrera virtuell nätverkstrafik till Azure-tjänster, vilket endast tillåter specifika Azure-tjänstresurser över tjänstslutpunkterna. Slutpunktsprinciper ger detaljerad åtkomstkontroll från den virtuella nätverkstrafiken till Azure-tjänsterna. Du kan läsa mer om tjänstslutpunktsprinciperna [här](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Stöder Azure Active Directory (Azure AD) slutpunkter för VNet-tjänsten?

Azure Active Directory (Azure AD) stöder inte tjänstslutpunkter internt. En fullständig lista över Azure Services som stöder slutpunkter för VNet-tjänsten kan ses [här](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Observera att taggen "Microsoft.AzureActiveDirectory" som visas under tjänster som stöder tjänstslutpunkter används för att stödja tjänstslutpunkter till ADLS Gen 1. För ADLS Gen 1 använder virtuell nätverksintegrering för Azure Data Lake Storage Gen1 slutpunktssäkerheten för den virtuella nätverkstjänsten mellan det virtuella nätverket och Azure Active Directory (Azure AD) för att generera ytterligare säkerhetsanspråk i åtkomsttoken. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto och tillåta åtkomst. Läs mer om [Azure Data Lake Store Gen 1 VNet-integrering](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Finns det några gränser för hur många slutpunkter för VNet-tjänsten som jag kan ställa in från mitt virtuella nätverk?
Det finns ingen gräns för det totala antalet VNet-tjänstslutpunkter i ett virtuellt nätverk. För en Azure-tjänstresurs (till exempel ett Azure Storage-konto) kan tjänster tillämpa gränser för antalet undernät som används för att skydda resursen. I följande tabell visas några exempelbegränsningar: 

|||
|---|---|
|Azure-tjänst| Begränsningar för VNet-regler|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> Gränserna är föremål för ändringar efter bedömning av Azure-tjänsten. Se respektive servicedokumentation för information om tjänster. 




  



