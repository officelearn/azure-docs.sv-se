---
title: "Virtuella Azure-nätverket vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor om Microsoft Azure-nätverk."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: jdial
ms.openlocfilehash: 2042bc44df7d3d61bf52d28a910dae1b125b9fdb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Vanliga frågor (FAQ) virtuella Azure-nätverket

## <a name="virtual-network-basics"></a>Grunderna i virtuella nätverk

### <a name="what-is-an-azure-virtual-network-vnet"></a>Vad är ett Azure Virtual Network (VNet)?
Ett Azure Virtual Network (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan använda Vnet att etablera och hantera virtuella privata nätverk (VPN) i Azure och, du kan också länka Vnet med andra Vnet i Azure, eller med din lokala IT-infrastruktur för att skapa lösningar för hybrid eller mellan platser. Varje VNet som du skapar har sin egen CIDR-block och kan länkas till andra Vnet och lokala nätverk som inte överlappar CIDR-block. Du kan ha kontroll över DNS-serverinställningarna för Vnet och segmentering av VNet i undernät.

Använd Vnet till:

* Skapa en dedikerad privata endast molnbaserad VNet ibland behöver en mellan lokala konfiguration för din lösning. När du skapar ett VNet, kan tjänster och virtuella datorer i ditt VNet kommunicera direkt och på ett säkert sätt med varandra i molnet. Detta håller trafiken på ett säkert sätt inom VNet, men ändå kan du konfigurera endpoint anslutningar för virtuella datorer och tjänster som kräver Internet-kommunikation som en del av din lösning.
* Utöka ditt datacenter med Vnet på ett säkert sätt, du kan skapa traditionella plats-till-plats (S2S) VPN om du vill skala datacenter-kapaciteten på ett säkert sätt. S2S VPN-anslutningar använder IPSEC för att tillhandahålla en säker anslutning mellan företagets VPN-gateway och Azure.
* Aktivera hybrid cloud scenarier Vnet ger dig möjlighet att hantera flera scenarier för hybrid. Du kan på ett säkert sätt ansluta molnbaserade program till alla typer av lokalt system, till exempel stordatorer och Unix-system.

### <a name="how-do-i-know-if-i-need-a-vnet"></a>Hur vet jag om jag behöver ett virtuellt nätverk?
Den [översikt över virtuella nätverk](virtual-networks-overview.md) artikeln innehåller en beslutstabell som hjälper dig att bestämma det bästa alternativet för ett design för dig.

### <a name="how-do-i-get-started"></a>Hur kommer jag igång?
Besök den [virtuella nätverk dokumentationen](https://docs.microsoft.com/azure/virtual-network/) att komma igång. Det här innehållet ger information om översikt och distribution för alla VNet-funktioner.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan jag använda Vnet utan korsanslutningar?
Ja. Du kan använda ett virtuellt nätverk utan att använda hybridanslutning. Detta är särskilt användbart om du vill köra Microsoft Windows Server Active Directory-domänkontrollanter och SharePoint-grupper i Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan jag utföra WAN-optimering mellan Vnet eller ett VNet och min lokala Datacenter?

Ja. Du kan distribuera en [WAN-optimering virtuell nätverksenhet](https://azure.microsoft.com/marketplace/?term=wan+optimization) från flera leverantörer via Azure Marketplace.

## <a name="configuration"></a>Konfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Vilka verktyg använder jag skapa en VNet?
Du kan använda följande verktyg för att skapa eller konfigurera ett virtuellt nätverk:

* Azure-portalen (för klassisk och Resource Manager VNets).
* En nätverket konfigurationsfil (netcfg - för klassiska Vnet). Finns det [konfigurera ett virtuellt nätverk med en konfigurationsfil för nätverket](virtual-networks-using-network-configuration-file.md) artikel.
* PowerShell (för klassisk och Resource Manager VNets).
* Azure CLI (för klassisk och Resource Manager VNets).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Vilka adressintervall kan jag använda min Vnet?
Alla IP-adressintervall som definierats i [RFC 1918](http://tools.ietf.org/html/rfc1918). Till exempel 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan jag ha offentliga IP-adresser i Mina Vnet?
Ja. Mer information om offentliga IP-adressintervall, finns det [offentliga IP-adressutrymme i ett virtuellt nätverk](virtual-networks-public-ip-within-vnet.md) artikel. Din offentliga IP-adresser är inte tillgänglig direkt från Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Finns det en gräns för antalet undernät i mitt virtuella nätverk?
Ja. Läs den [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) artikeln för information. Undernät adressutrymmen får inte överlappa varandra.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Finns det några begränsningar med hjälp av IP-adresser inom dessa undernät?
Ja. Azure reserverar vissa IP-adresser inom varje undernät. De första och sista IP-adresserna i undernäten är reserverade för protokollöverensstämmelse, tillsammans med ytterligare 3 adresser som används för Azure-tjänster.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hur små och hur stor kan vara Vnet och undernät?
Minsta undernätet vi stöder är en /29 och den största är en/8 som (med CIDR-subnätsdefinitioner).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan jag använda min virtuella lokala nätverk till Azure med hjälp av Vnet?
Nej. Vnet är nivå 3 överlägg. Azure stöder inte någon semantik för Layer-2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan jag ange anpassade routningsprinciper på Mina Vnet och undernät?
Ja. Du kan använda användaren definierat routning (UDR). Mer information om UDR finns [användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Stöder Vnet multicast eller broadcast?
Nej. Vi stöder inte multicast eller broadcast.

### <a name="what-protocols-can-i-use-within-vnets"></a>Vilka protokoll inom Vnet kan jag använda?
Du kan använda TCP och UDP ICMP TCP/IP-protokoll inom Vnet. Unicast stöds inom Vnet, med undantag för dynamiska värden Configuration Protocol (DHCP) via Unicast (källport UDP/68 / mål port UDP/67). Multicast, broadcast, kapslas in IP-i-IP-paket och allmänna Routing Encapsulation (GRE) paket blockeras inom Vnet. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan jag Pinga standard-routrar inom ett VNet?
Nej.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan jag använda tracert för att diagnosticera anslutningen?
Nej.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan jag lägga till undernät efter att VNet har skapats?
Ja. Undernät kan läggas till Vnet när som helst så länge undernätsadressen inte är en del av ett annat undernät i VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan jag ändra storleken på mitt undernät när jag har skapat den?
Ja. Du kan lägga till, ta bort, expandera eller förminska ett undernät om det finns inga virtuella datorer eller tjänster som distribueras inom den.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Kan jag ändra undernät när jag skapar dem?
Ja. Du kan lägga till, ta bort och ändra CIDR-block som används av ett virtuellt nätverk.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Kan jag ansluta till internet om jag använder Mina tjänster i ett virtuellt nätverk?
Ja. Alla tjänster som distribueras inom ett virtuellt nätverk kan ansluta till internet. Varje distribuerad i Azure-Molntjänsten har ett offentligt adresserbara VIP som tilldelats. Du måste definiera inkommande slutpunkter för PaaS-roller och slutpunkter för virtuella datorer för att aktivera dessa tjänster att godkänna anslutningar från internet.

### <a name="do-vnets-support-ipv6"></a>Vnet som har stöd för IPv6?
Nej. Du kan inte använda IPv6 med VNets just nu.

### <a name="can-a-vnet-span-regions"></a>Ett virtuellt nätverk kan omfatta regioner?
Nej. Ett virtuellt nätverk är begränsad till en enda region.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan jag ansluta ett virtuellt nätverk till ett annat VNet i Azure?
Ja. Du kan ansluta ett virtuellt nätverk till ett annat virtuellt nätverk med hjälp av:
- En Azure VPN-Gateway. Läs den [konfigurera VNet-till-VNet-anslutningen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artikeln för information. 
- VNet-peering. Läs den [VNet-peering översikt](virtual-network-peering-overview.md) artikeln för information.

## <a name="name-resolution-dns"></a>Name Resolution (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Vad är DNS-alternativ för Vnet?
Använd tabellen beslut på den [namnmatchning för virtuella datorer och Rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md) att guida alla DNS-alternativen tillgängliga.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan jag ange DNS-servrar för ett virtuellt nätverk?
Ja. Du kan ange IP-adresser för DNS-servrar i inställningarna för virtuella nätverk. Detta används som standard-DNS-servrar för alla virtuella datorer i virtuella nätverk.

### <a name="how-many-dns-servers-can-i-specify"></a>Hur många DNS-servrar kan ange?
Referens för den [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) artikeln för information.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan jag ändra Mina DNS-servrar när jag har skapat nätverket?
Ja. Du kan ändra DNS-serverlistan för din VNet när som helst. Om du ändrar DNS-serverlistan behöver du starta om alla de virtuella datorerna i ditt virtuella nätverk för att hämta den nya DNS-servern.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Vad är Azure-tillhandahållna DNS och fungerar det med VNets?
Azure-tillhandahållna DNS är en DNS-tjänsten för flera innehavare som erbjuds av Microsoft. Azure registrerar alla virtuella datorer och molnet tjänstinstanser roll i den här tjänsten. Den här tjänsten tillhandahåller namnmatchning genom värdnamn för virtuella datorer och rollinstanser som finns i samma molntjänst och FQDN för virtuella datorer och rollinstanser i samma virtuella nätverk. Läs den [namnmatchning för virtuella datorer och Rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md) artikeln om du vill veta mer om DNS.

> [!NOTE]
> Det finns en begränsning just nu på de första 100 molntjänsterna i ett virtuellt nätverk för flera innehavare namnmatchning med hjälp av Azure-tillhandahållna DNS. Den här begränsningen gäller inte om du använder DNS-servern.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Kan jag åsidosätta DNS-inställningarna på en per VM / service bas?
Ja. Du kan ange DNS-servrar för tjänst per moln att åsidosätta standardinställningarna för nätverk. Vi rekommenderar dock att du använder över hela nätverket DNS så mycket som möjligt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan jag använda min egen DNS-suffix?
Nej. Du kan inte ange en anpassad DNS-suffix för din Vnet.

## <a name="connecting-virtual-machines"></a>Ansluta virtuella datorer

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan jag distribuera virtuella datorer till ett virtuellt nätverk?
Ja. Alla nätverksgränssnitt (NIC) ansluten till en virtuell dator distribueras via Resource Manager-distributionsmodellen måste vara ansluten till ett virtuellt nätverk. Virtuella datorer som distribueras via den klassiska distributionsmodellen kan du vara ansluten till ett virtuellt nätverk.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Vilka är de olika typerna av IP-adresser som jag kan tilldela virtuella datorer?
* **Privat:** tilldelas varje nätverkskort på varje virtuell dator. Adressen som har tilldelats med hjälp av antingen metoden statisk eller dynamisk. Privata IP-adresser tilldelas från det intervall som du angav i Undernätverksinställningarna för ditt VNet. Resurser som distribueras via den klassiska distributionsmodellen tilldelas privata IP-adresser, även om de inte är ansluten till ett virtuellt nätverk. En privat IP-adress med metoden dynamiska fortfarande kopplad till en resurs tills resursen tas bort (virtuella datorer eller tjänst i molnet distributionsplatser). En privat IP-adress med metoden dynamiska kan ändras när en virtuell dator startas efter att ha varit i tillståndet Stoppad (frigjord). En privat IP-adress med den statiska metoden fortfarande kopplad till en resurs tills resursen tas bort. Om du behöver säkerställa att den privata IP-adressen för en resurs aldrig ändras tills resursen tas bort kan du tilldela en privat IP-adress med den statiska metoden.
* **Publik:** eventuellt som tilldelats till nätverkskort som är kopplade till virtuella datorer som distribueras via Azure Resource Manager-distributionsmodellen. Adressen som kan tilldelas med allokeringsmetoden statisk eller dynamisk. Alla virtuella datorer och molntjänster rollinstanser som distribuerats via den klassiska distributionsmodellen finns i en molntjänst som är tilldelade en *dynamisk*, offentliga virtuella IP-adressen. En offentlig *Statiska* IP-adress, som kallas en [reserverad IP-adress](virtual-networks-reserved-public-ip.md), kan också tilldelas som en VIP. Du kan tilldela enskilda virtuella datorer eller molntjänster rollinstanser som distribuerats via den klassiska distributionsmodellen offentliga IP-adresser. Dessa adresser kallas [nivå offentliga IP-instans (går](virtual-networks-instance-level-public-ip.md) adresser och kan tilldelas dynamiskt.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan jag reservera en privat IP-adress för en virtuell dator som jag skapar vid ett senare tillfälle?
Nej. Du kan inte reservera en privat IP-adress. Om en privat IP-adress tilldelas den till en virtuell dator eller roll-instans av DHCP-servern. Den virtuella datorn kanske eller kanske inte det som du vill att den privata IP-adressen som ska tilldelas. Du kan dock ändra privata IP-adressen för en dator som redan har skapat till alla tillgängliga privata IP-adress.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Gör privat förändring av IP-adresser för virtuella datorer i ett virtuellt nätverk?
Det beror på. Dynamisk privata IP-adresser fortfarande med en virtuell dator till dess Stoppad (frigjord) eller tas bort. Statisk privat IP-adresser inte är publicerat från en virtuell dator tills det tas bort.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan jag manuellt tilldela IP-adresser till nätverkskort i operativsystemet VM?
Ja, men det rekommenderas inte. Manuellt ändra IP-adressen för ett nätverkskort i en virtuell dators operativsystem eventuellt kan orsaka förlorar anslutningen till den virtuella datorn om ändrar IP-adress som tilldelats ett nätverkskort i Azure-dator.

### <a name="what-happens-to-my-ip-addresses-if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system"></a>Vad händer med IP-adresser om jag stoppa en molntjänst distributionsplatsen eller avstängning för en virtuell dator från inom operativsystemet?
Det finns inget. IP-adresser (offentlig VIP, offentliga och privata) fortsätter vara tilldelade till distributionsplatsen för molnet tjänsten eller VM. Dynamiska adresser släpps endast om en virtuell dator har stoppats (frigjorts) eller tagits bort, eller en distributionsplats i molnet tjänsten tas bort. Klicka på den **stoppa** knappen för en virtuell dator i Azure portal anger dess status till Stoppad (frigjord). I det här fallet förlorar den virtuella datorn sin IP-adresser.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Kan jag flytta virtuella datorer från ett undernät till ett annat undernät i ett virtuellt nätverk utan att distribuera igen?
Ja. Du hittar mer information finns i den [hur du flyttar en virtuell dator eller roll-instans till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md) artikel.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan jag konfigurera en statisk MAC-adress för den virtuella datorn?
Nej. En MAC-adress kan inte vara statiskt konfigurerade.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>MAC-adressen förblir detsamma för den virtuella datorn när den har skapats?
Ja, MAC-adress är densamma för en virtuell dator distribueras via både Resource Manager och klassiska distributionsmodeller tills det tas bort. MAC-adressen släpptes tidigare, om den virtuella datorn har stoppats (frigjorts), men nu MAC-adressen sparas även om den virtuella datorn är i tillståndet frigjord.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan jag ansluta till internet från en virtuell dator i ett virtuellt nätverk?
Ja. Alla virtuella datorer och molntjänster rollinstanser som distribuerats i ett virtuellt nätverk kan ansluta till Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-tjänster som ansluter till Vnet

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan jag använda Azure App Service Web Apps med ett virtuellt nätverk?
Ja. Du kan distribuera webbprogram i ett VNet med en ASE (Apptjänstmiljö). Alla webbprogram kan på ett säkert sätt ansluta och komma åt resurser i ditt virtuella Azure-om du har en punkt-till-plats-anslutning som konfigurerats för ditt VNet. Mer information finns i följande artiklar:

* [Skapa Webbappar i Apptjänst-miljö](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md)
* [Med Web Apps VNet-integrering och Hybridanslutningar](../app-service/web-sites-integrate-with-vnet.md#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan jag distribuera molntjänster med webb- och arbetsroller roller (PaaS) i ett virtuellt nätverk?
Ja. (Valfritt) kan du distribuera molntjänster rollinstanser inom Vnet. Gör anger du namnet på VNet och mappningar för roll /-undernätet i konfigurationsavsnittet nätverk i tjänstkonfigurationen av. Du behöver inte uppdatera alla dina binärfiler.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Kan jag ansluta en virtuell dator skala ange (VMSS) till ett virtuellt nätverk?
Ja. En VMSS måste du ansluta till ett virtuellt nätverk.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan jag flytta Mina tjänster till och från Vnet?
Nej. Du kan inte flytta tjänster till och från Vnet. Du måste ta bort och distribuera tjänsten för att flytta den till ett annat virtuellt nätverk.

## <a name="security"></a>Säkerhet

### <a name="what-is-the-security-model-for-vnets"></a>Vad är säkerhetsmodellen för Vnet?
Vnet är helt isolerade från varandra och andra tjänster i Azure-infrastrukturen. Ett virtuellt nätverk är en förtroendegräns.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan jag begränsa inkommande eller utgående trafikflöde till VNet-anslutna resurser?
Ja. Du kan använda [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md) till enskilda undernät inom ett VNet nätverkskort som är kopplad till ett virtuellt nätverk eller båda.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan jag implementera en brandvägg mellan VNet-anslutna resurser?
Ja. Du kan distribuera en [brandväggen virtuell nätverksenhet](https://azure.microsoft.com/en-us/marketplace/?term=firewall) från flera leverantörer via Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Finns det information om hur du skyddar Vnet?
Ja. Finns det [översikt över Azure Network Security](../security/security-network-overview.md) artikeln för information.

## <a name="apis-schemas-and-tools"></a>API: er, scheman och verktyg

### <a name="can-i-manage-vnets-from-code"></a>Kan jag hantera Vnet från kod?
Ja. Du kan använda REST API: er för VNets i den [Azure Resource Manager](https://msdn.microsoft.com/library/mt163658.aspx) och [klassisk (servicehantering)](http://go.microsoft.com/fwlink/?LinkId=296833)) distributionsmodeller.

### <a name="is-there-tooling-support-for-vnets"></a>Finns det verktygsuppsättning stöd för Vnet?
Ja. Lär dig mer om hur du använder:
- Azure portal för att distribuera Vnet via den [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) och [klassiska](virtual-networks-create-vnet-classic-pportal.md) distributionsmodeller.
- PowerShell för att hantera Vnet som distribueras via den [Resource Manager](/powershell/resourcemanager/azurerm.network/v3.1.0/azurerm.network.md) och [klassiska](/powershell/module/azure/?view=azuresmps-3.7.0) distributionsmodeller.
- Den [Azure-kommandoradsgränssnittet (CLI)](../virtual-machines/azure-cli-arm-commands.md#azure-network-commands-to-manage-network-resources) att hantera Vnet som distribueras via båda distributionsmodellerna.  
