---
title: "Virtuella Azure-nätverket vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor om Microsoft Azure-nätverk."
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
ms.date: 03/01/2018
ms.author: jdial
ms.openlocfilehash: 8800dc59306c349daba8f4d9703e0c713eed06ec
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Vanliga frågor (FAQ) virtuella Azure-nätverket

## <a name="virtual-network-basics"></a>Grunderna i virtuella nätverk

### <a name="what-is-an-azure-virtual-network-vnet"></a>Vad är ett Azure Virtual Network (VNet)?
Ett Azure Virtual Network (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan använda Vnet att etablera och hantera virtuella privata nätverk (VPN) i Azure och, du kan också länka Vnet med andra Vnet i Azure, eller med din lokala IT-infrastruktur för att skapa lösningar för hybrid eller mellan platser. Varje VNet som du skapar har sin egen CIDR-block och kan länkas till andra Vnet och lokala nätverk som inte överlappar CIDR-block. Du kan ha kontroll över DNS-serverinställningarna för Vnet och segmentering av VNet i undernät.

Använd Vnet till:

* Skapa en dedikerad privata endast molnbaserad VNet ibland behöver en mellan lokala konfiguration för din lösning. När du skapar ett VNet, kan tjänster och virtuella datorer i ditt VNet kommunicera direkt och på ett säkert sätt med varandra i molnet. Du kan fortfarande konfigurera endpoint anslutningar för virtuella datorer och tjänster som kräver Internet-kommunikation som en del av din lösning.
* Utöka ditt datacenter med Vnet på ett säkert sätt, du kan skapa traditionella plats-till-plats (S2S) VPN om du vill skala datacenter-kapaciteten på ett säkert sätt. S2S VPN-anslutningar använder IPSEC för att tillhandahålla en säker anslutning mellan företagets VPN-gateway och Azure.
* Aktivera hybrid cloud scenarier Vnet ger dig möjlighet att hantera flera scenarier för hybrid. Du kan på ett säkert sätt ansluta molnbaserade program till alla typer av lokalt system, till exempel stordatorer och Unix-system.

### <a name="how-do-i-get-started"></a>Hur kommer jag igång?
Besök den [virtuella nätverk dokumentationen](https://docs.microsoft.com/azure/virtual-network/) att komma igång. Det här innehållet ger information om översikt och distribution för alla funktioner för virtuella nätverk.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan jag använda Vnet utan korsanslutningar?
Ja. Du kan använda ett virtuellt nätverk utan att ansluta till din lokala. Du kan till exempel köra Microsoft Windows Server Active Directory-domänkontrollanter och SharePoint-servergrupper endast i ett Azure VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan jag utföra WAN-optimering mellan Vnet eller ett VNet och min lokala Datacenter?

Ja. Du kan distribuera en [WAN-optimering virtuell nätverksenhet](https://azure.microsoft.com/marketplace/?term=wan+optimization) från flera leverantörer via Azure Marketplace.

## <a name="configuration"></a>Konfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Vilka verktyg använder jag skapa en VNet?
Du kan använda följande verktyg för att skapa eller konfigurera ett virtuellt nätverk:

* Azure Portal
* PowerShell
* Azure CLI
* En nätverket konfigurationsfil (netcfg - för klassiska Vnet). Finns det [konfigurera ett virtuellt nätverk med en konfigurationsfil för nätverket](virtual-networks-using-network-configuration-file.md) artikel.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Vilka adressintervall kan jag använda min Vnet?
Alla IP-adressintervall som definierats i [RFC 1918](http://tools.ietf.org/html/rfc1918). Till exempel 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan jag ha offentliga IP-adresser i Mina Vnet?
Ja. Läs mer om offentliga IP-adressintervall, [skapa ett virtuellt nätverk](virtual-network-manage-network.md#create-a-virtual-network). Offentliga IP-adresser är inte tillgänglig direkt från internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Finns det en gräns för antalet undernät i mitt virtuella nätverk?
Ja. Se [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) mer information. Undernät adressutrymmen får inte överlappa varandra.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Finns det några begränsningar med hjälp av IP-adresser inom dessa undernät?
Ja. Azure reserverar vissa IP-adresser inom varje undernät. Första och sista IP-adresser för varje undernät är reserverade för överensstämmelse med protokollet, tillsammans med x.x.x.1 x.x.x.3 adresser för varje undernät som används för Azure-tjänster.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hur små och hur stor kan vara Vnet och undernät?
Minsta stöds undernätet är /29 och den största/8 som (med CIDR-subnätsdefinitioner).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan jag använda min virtuella lokala nätverk till Azure med hjälp av Vnet?
Nej. Vnet är nivå 3 överlägg. Azure stöder inte någon semantik för Layer-2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan jag ange anpassade routningsprinciper på Mina Vnet och undernät?
Ja. Du kan skapa en routingtabell och koppla den till ett undernät. Mer information om routning i Azure finns [routning: översikt](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Stöder Vnet multicast eller broadcast?
Nej. Multicast- och broadcast stöds inte.

### <a name="what-protocols-can-i-use-within-vnets"></a>Vilka protokoll inom Vnet kan jag använda?
Du kan använda TCP och UDP ICMP TCP/IP-protokoll inom Vnet. Unicast stöds inom Vnet, med undantag för DHCP Dynamic Host Configuration Protocol () via Unicast (källport UDP/68 / mål port UDP/67). Multicast, broadcast, kapslas in IP-i-IP-paket och allmänna Routing Encapsulation (GRE) paket blockeras inom Vnet. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan jag Pinga standard-routrar inom ett VNet?
Nej.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan jag använda tracert för att diagnosticera anslutningen?
Nej.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan jag lägga till undernät efter att VNet har skapats?
Ja. Undernät kan läggas till Vnet när som helst så länge undernät-adressintervallet är inte en del av ett annat undernät och det finns utrymme kvar i det virtuella nätverkets adressintervall.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan jag ändra storleken på mitt undernät när jag har skapat den?
Ja. Du kan lägga till, ta bort, expandera eller förminska ett undernät om det finns inga virtuella datorer eller tjänster som distribueras inom den.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Kan jag ändra undernät när jag skapar dem?
Ja. Du kan lägga till, ta bort och ändra CIDR-block som används av ett virtuellt nätverk.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Om jag använder Mina tjänster i ett virtuellt nätverk kan ansluta till internet?
Ja. Alla tjänster som distribueras inom ett virtuellt nätverk kan ansluta utgående till internet. Mer information om utgående internet-anslutningar i Azure finns [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill ansluta till inkommande till en resurs som har distribuerats via Resource Manager, måste resursen ha en offentlig IP-adress som tilldelats. Läs mer om offentliga IP-adresser i [offentliga IP-adresser](virtual-network-public-ip-address.md). Varje Azure-molntjänst som är distribuerad i Azure har ett offentligt adresserbara VIP som tilldelats. Du anger inkommande slutpunkter för PaaS-roller och slutpunkter för virtuella datorer för att aktivera dessa tjänster att godkänna anslutningar från internet.

### <a name="do-vnets-support-ipv6"></a>Vnet som har stöd för IPv6?
Nej. Du kan inte använda IPv6 med VNets just nu. Du kan dock, tilldela IPv6-adresser till Azure belastningsutjämnare för att läsa in balansera virtuella datorer. Mer information finns i [översikt över IPv6 för Azure-belastningsutjämnaren](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Ett virtuellt nätverk kan omfatta regioner?
Nej. Ett virtuellt nätverk är begränsad till en enda region. Ett virtuellt nätverk gör det kan dock span tillgänglighet zoner. Mer information om tillgänglighetszoner finns i [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Du kan ansluta virtuella nätverk i olika regioner med virtuella nätverk peering. Mer information finns i [peering översikt över virtuella nätverk](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan jag ansluta ett virtuellt nätverk till ett annat VNet i Azure?
Ja. Du kan ansluta ett virtuellt nätverk till ett annat virtuellt nätverk med hjälp av antingen:
- **Virtuellt nätverk peering**: Mer information finns i [VNet-peering översikt](virtual-network-peering-overview.md)
- **En Azure VPN-Gateway**: Mer information finns i [konfigurera VNet-till-VNet-anslutningen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Name Resolution (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Vad är DNS-alternativ för Vnet?
Använd tabellen beslut på den [namnmatchning för virtuella datorer och Rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md) att guida alla DNS-alternativen tillgängliga.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan jag ange DNS-servrar för ett virtuellt nätverk?
Ja. Du kan ange IP-adresser för DNS-servrar i inställningarna för virtuella nätverk. Inställningen används som standard-DNS-servrar för alla virtuella datorer i virtuella nätverk.

### <a name="how-many-dns-servers-can-i-specify"></a>Hur många DNS-servrar kan ange?
Referens [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan jag ändra Mina DNS-servrar när jag har skapat nätverket?
Ja. Du kan ändra DNS-serverlistan för din VNet när som helst. Om du ändrar DNS-serverlistan behöver du starta om alla de virtuella datorerna i ditt virtuella nätverk för att hämta den nya DNS-servern.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Vad är Azure-tillhandahållna DNS och fungerar det med VNets?
Azure-tillhandahållna DNS är en DNS-tjänsten för flera innehavare som erbjuds av Microsoft. Azure registrerar alla virtuella datorer och molnet tjänstinstanser roll i den här tjänsten. Den här tjänsten tillhandahåller namnmatchning genom värdnamn för virtuella datorer och rollinstanser som finns i samma molntjänst och FQDN för virtuella datorer och rollinstanser i samma virtuella nätverk. Mer information om DNS finns [namnmatchning för virtuella datorer och molntjänster rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Det finns en begränsning för de första 100 molntjänsterna i ett virtuellt nätverk för mellan klient-namnmatchning med hjälp av Azure-tillhandahållna DNS. Den här begränsningen gäller inte om du använder DNS-servern.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Kan jag åsidosätta DNS-inställningarna för tjänst per-VM eller molnet?
Ja. Du kan ange DNS-servrar per virtuell dator eller molnet tjänsten åsidosätter standardinställningarna för nätverket. Det rekommenderas dock att du använder över hela nätverket DNS så mycket som möjligt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan jag använda min egen DNS-suffix?
Nej. Du kan inte ange en anpassad DNS-suffix för din Vnet.

## <a name="connecting-virtual-machines"></a>Ansluta virtuella datorer

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan jag distribuera virtuella datorer till ett virtuellt nätverk?
Ja. Alla nätverksgränssnitt (NIC) ansluten till en virtuell dator distribueras via Resource Manager-distributionsmodellen måste vara ansluten till ett virtuellt nätverk. Virtuella datorer som distribueras via den klassiska distributionsmodellen kan du vara ansluten till ett virtuellt nätverk.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Vilka är de olika typerna av IP-adresser som jag kan tilldela virtuella datorer?
* **Privat:** tilldelas varje nätverkskort på varje virtuell dator. Adressen som har tilldelats med hjälp av antingen metoden statisk eller dynamisk. Privata IP-adresser tilldelas från det intervall som du angav i Undernätverksinställningarna för ditt VNet. Resurser som distribueras via den klassiska distributionsmodellen tilldelas privata IP-adresser, även om de inte är ansluten till ett virtuellt nätverk. Beteendet för allokeringsmetoden är olika beroende på om en resurs har distribuerats med Resource Manager eller klassiska distributionsmodellen: 

  - **Hanteraren för filserverresurser**: en privat IP-adress med dynamiska eller statiska metoden fortfarande kopplad till en virtuell dator (Resource Manager) tills resursen tas bort. Skillnaden är att du väljer adressen som ska tilldelas när du använder statisk och Azure väljer när du använder dynamiska. 
  - **Klassiska**: en privat IP-adress med metoden dynamiska kan ändras när en virtuell dator (klassisk) VM startas efter att ha varit i tillståndet Stoppad (frigjord). Tilldela en privat IP-adress med den statiska metoden om du behöver se till att den privata IP-adressen för en resurs som distribueras via den klassiska distributionsmodellen aldrig ändras.

* **Publik:** eventuellt som tilldelats till nätverkskort som är kopplade till virtuella datorer som distribueras via Azure Resource Manager-distributionsmodellen. Adressen som kan tilldelas med allokeringsmetoden statisk eller dynamisk. Alla virtuella datorer och molntjänster rollinstanser som distribuerats via den klassiska distributionsmodellen finns i en molntjänst som är tilldelade en *dynamisk*, offentliga virtuella IP-adressen. En offentlig *Statiska* IP-adress, som kallas en [reserverad IP-adress](virtual-networks-reserved-public-ip.md), kan också tilldelas som en VIP. Du kan tilldela enskilda virtuella datorer eller molntjänster rollinstanser som distribuerats via den klassiska distributionsmodellen offentliga IP-adresser. Dessa adresser kallas [nivå offentliga IP-instans (går](virtual-networks-instance-level-public-ip.md) adresser och kan tilldelas dynamiskt.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan jag reservera en privat IP-adress för en virtuell dator som jag skapar vid ett senare tillfälle?
Nej. Du kan inte reservera en privat IP-adress. Om det finns en privat IP-adress tilldelas den till en virtuell dator eller roll-instans av DHCP-servern. Den virtuella datorn kanske eller kanske inte det som du vill tilldela den privata IP-adressen. Du kan dock ändra privata IP-adressen för en dator som redan har skapat till alla tillgängliga privata IP-adress.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Gör privat förändring av IP-adresser för virtuella datorer i ett virtuellt nätverk?
Det beror på. Om den virtuella datorn har distribuerats via Hanteraren för filserverresurser, Nej, oavsett om IP-adress har tilldelats med allokeringsmetoden statisk eller dynamisk. Om den virtuella datorn har distribuerats via den klassiska distributionsmodellen, kan dynamiska IP-adresser ändras när en virtuell dator startas efter att ha varit i tillståndet Stoppad (frigjord). Adressen har getts ut från en virtuell dator distribueras med antingen distributionsmodell när den virtuella datorn tas bort.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan jag manuellt tilldela IP-adresser till nätverkskort i operativsystemet VM?
Ja, men det rekommenderas inte såvida inte behövs, till exempel när tilldela flera IP-adresser till en virtuell dator. Mer information finns i [att lägga till flera IP-adresser till en virtuell dator](virtual-network-multiple-ip-addresses-portal.md#os-config). Om IP-adressen till en Azure-NIC kopplat till en virtuell dator ändringar och IP-adressen i VM-operativsystemet är annorlunda kan du förlora anslutningen till den virtuella datorn.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Vad händer med IP-adresser om jag stoppa en molntjänst distributionsplatsen eller avstängning för en virtuell dator från inom operativsystemet?
Det finns inget. IP-adresser (offentlig VIP, offentliga och privata) fortsätter vara tilldelade till distributionsplatsen för molnet tjänsten eller VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Kan jag flytta virtuella datorer från ett undernät till ett annat undernät i ett virtuellt nätverk utan att omdistribuera?
Ja. Du hittar mer information finns i den [hur du flyttar en virtuell dator eller roll-instans till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md) artikel.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan jag konfigurera en statisk MAC-adress för den virtuella datorn?
Nej. En MAC-adress kan inte vara statiskt konfigurerade.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>MAC-adressen förblir detsamma för den virtuella datorn när den har skapats?
Ja, MAC-adress är densamma för en virtuell dator distribueras via både Resource Manager och klassiska distributionsmodeller tills det tas bort. MAC-adressen släpptes tidigare, om den virtuella datorn har stoppats (frigjorts), men nu MAC-adressen sparas även om den virtuella datorn är i tillståndet frigjord.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan jag ansluta till internet från en virtuell dator i ett virtuellt nätverk?
Ja. Alla virtuella datorer och molntjänster rollinstanser som distribuerats i ett virtuellt nätverk kan ansluta till Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-tjänster som ansluter till Vnet

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan jag använda Azure App Service Web Apps med ett virtuellt nätverk?
Ja. Du kan distribuera webbprogram i ett VNet med en ASE (Apptjänstmiljö). Om du har en punkt-till-plats-anslutning som konfigurerats för ditt VNet alla webbprogram på ett säkert sätt ansluta och komma åt resurser i VNet. Mer information finns i följande artiklar:

* [Skapa Webbappar i Apptjänst-miljö](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Med Web Apps VNet-integrering och Hybridanslutningar](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan jag distribuera molntjänster med webb- och arbetsroller roller (PaaS) i ett virtuellt nätverk?
Ja. (Valfritt) kan du distribuera molntjänster rollinstanser inom Vnet. Gör anger du namnet på VNet och mappningar för roll /-undernätet i konfigurationsavsnittet nätverk i tjänstkonfigurationen av. Du behöver inte uppdatera alla dina binärfiler.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Kan jag ansluta en virtuell dator skala ange (VMSS) till ett virtuellt nätverk?
Ja. En VMSS måste du ansluta till ett virtuellt nätverk.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Finns det en fullständig lista över Azure services att kan jag distribuera resurser i ett virtuellt nätverk?

Ja, mer information finns i [integrering av virtuella nätverk för Azure-tjänster](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Vilka Azure PaaS-resurser kan jag begränsa åtkomst till från ett VNet?

Resurser som har distribuerats via vissa Azure PaaS-tjänster (till exempel Azure Storage och Azure SQL Database), kan begränsa nätverksåtkomst till endast resurser i ett VNet med hjälp av slutpunkter för virtuellt nätverk. Mer information finns i [översikt över virtuella nätverk service slutpunkter](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan jag flytta Mina tjänster till och från Vnet?
Nej. Du kan inte flytta tjänster till och från Vnet. För att flytta en resurs till ett annat VNet, måste du ta bort och distribuera om resursen.

## <a name="security"></a>Säkerhet

### <a name="what-is-the-security-model-for-vnets"></a>Vad är säkerhetsmodellen för Vnet?
Vnet är isolerade från varandra och andra tjänster i Azure-infrastrukturen. Ett virtuellt nätverk är en förtroendegräns.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan jag begränsa inkommande eller utgående trafikflöde till VNet-anslutna resurser?
Ja. Du kan använda [Nätverkssäkerhetsgrupper](security-overview.md) till enskilda undernät inom ett VNet nätverkskort som är kopplad till ett virtuellt nätverk eller båda.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan jag implementera en brandvägg mellan VNet-anslutna resurser?
Ja. Du kan distribuera en [brandväggen virtuell nätverksenhet](https://azure.microsoft.com/marketplace/?term=firewall) från flera leverantörer via Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Finns det information om hur du skyddar Vnet?
Ja. Mer information finns i [översikt över Azure Network Security](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API: er, scheman och verktyg

### <a name="can-i-manage-vnets-from-code"></a>Kan jag hantera Vnet från kod?
Ja. Du kan använda REST API: er för VNets i den [Azure Resource Manager](/rest/api/virtual-network) och [klassisk (servicehantering)](http://go.microsoft.com/fwlink/?LinkId=296833) distributionsmodeller.

### <a name="is-there-tooling-support-for-vnets"></a>Finns det verktygsuppsättning stöd för Vnet?
Ja. Lär dig mer om hur du använder:
- Azure portal för att distribuera Vnet via den [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) och [klassiska](virtual-networks-create-vnet-classic-pportal.md) distributionsmodeller.
- PowerShell för att hantera Vnet som distribueras via den [Resource Manager](/powershell/module/azurerm.network) och [klassiska](/powershell/module/azure/?view=azuresmps-3.7.0) distributionsmodeller.
- Azure-kommandoradsgränssnittet (CLI) för att distribuera och hantera Vnet som distribueras via den [Resource Manager](/cli/azure/network/vnet) och [klassiska](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) distributionsmodeller.  
