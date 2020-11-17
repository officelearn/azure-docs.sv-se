---
title: Vanliga frågor och svar om Azure Firewall
description: Vanliga frågor och svar om Azure-brandväggen. En hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 3e6ea6692a81a06bbf3180904dfb465a88b105d1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653427"
---
# <a name="azure-firewall-faq"></a>Vanliga frågor och svar om Azure Firewall

## <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Vilka funktioner stöds i Azure Firewall?

Information om Azure Firewall-funktioner finns i [Azure Firewall-funktioner](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Vad är en typisk distributions modell för Azure-brandväggen?

Du kan distribuera Azure Firewall på alla virtuella nätverk, men det vanliga är att kunder distribuerar brandväggen på ett centralt virtuellt nätverk och peer-kopplar andra virtuella nätverk till den i en ”nav och eker”-modell. Du kan sedan ange standard vägen från de peer-peer-virtuella nätverken så att de pekar på det här centrala brand Väggs nätverket. Global VNet-peering stöds, men det rekommenderas inte på grund av eventuella problem med prestanda och latens i flera regioner. För att få bästa prestanda bör du distribuera en brandvägg per region.

Fördelen med den här modellen är att du får central kontroll över flera virtuella eker-nätverk för olika prenumerationer. Det kostar också besparingar eftersom du inte behöver distribuera en brand vägg i varje VNet separat. Kostnadsbesparingarna bör ställas i jämförelse med kostnaden för peering utifrån kundens trafikmönster.

## <a name="how-can-i-install-the-azure-firewall"></a>Hur kan jag installera Azure-brandväggen?

Du kan konfigurera Azure-brandväggen med hjälp av Azure Portal, PowerShell, REST API eller med hjälp av mallar. Se [självstudie: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md) för stegvisa instruktioner.

## <a name="what-are-some-azure-firewall-concepts"></a>Vilka är några Azure Firewall-koncept?

Azure-brandväggen stöder regler och regel samlingar. En regel samling är en uppsättning regler som delar samma ordning och prioritet. Regel samlingar körs i prioritetsordning. Nätverks regel samlingar är högre prioritet än program regel samlingar och alla regler avslutas.

Det finns tre typer av regel samlingar:

* *Program regler*: Konfigurera fullständigt kvalificerade domän namn (FQDN) som kan nås från ett undernät.
* *Nätverks regler*: Konfigurera regler som innehåller käll adresser, protokoll, mål portar och mål adresser.
* *NAT-regler*: Konfigurera DNAt-regler för att tillåta inkommande Internet anslutningar.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Stöder Azure-brandväggen inkommande trafik filtrering?

Azure-brandväggen stöder inkommande och utgående filtrering. Inkommande skydd används vanligt vis för protokoll som inte är HTTP/S. Till exempel RDP-, SSH-och FTP-protokoll. För bästa inkommande HTTP/S-skydd använder du en brand vägg för webbaserade program, till exempel [Azure Web Application Firewall (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Vilka loggnings-och analys tjänster stöds av Azure-brandväggen?

Azure-brandväggen är integrerad med Azure Monitor för att visa och analysera brand Väggs loggar. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hubs. De kan analyseras i Log Analytics eller av olika verktyg som Excel och Power BI. Mer information finns i [Självstudier: övervaka Azure Firewall-loggar](./firewall-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hur fungerar Azure Firewall annorlunda jämfört med befintliga tjänster som NVA på Marketplace?

Azure Firewall är en grundläggande brand Väggs tjänst som kan hantera vissa kund scenarier. Det förväntas att du har en blandning av NVA och Azure-brandväggen från tredje part. Att arbeta bättre tillsammans är en grundläggande prioritet.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure-brandväggen?

Brand väggen för webbaserade program (WAF) är en funktion i Application Gateway som tillhandahåller centraliserat inkommande skydd av dina webb program från vanliga sårbarheter och sårbarheter. Azure-brandväggen ger inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP), utgående skydd på nätverks nivå för alla portar och protokoll och skydd på program nivå för utgående HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Vad är skillnaden mellan nätverks säkerhets grupper (NSG: er) och Azure-brandväggen?

Azure Firewall service kompletterar funktioner för nätverks säkerhets grupper. Tillsammans ger de bättre nätverks säkerhet i "försvar och djupgående". Nätverkssäkerhetsgrupper innehåller distribuerad filtrering av nätverkstrafik som begränsar trafiken till resurser i virtuella nätverk i varje prenumeration. Azure-brandväggen är en fullständigt tillstånds känslig, centraliserad nätverks brand vägg som en tjänst, som tillhandahåller skydd på Nätverks-och program nivå för olika prenumerationer och virtuella nätverk.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Stöds nätverks säkerhets grupper (NSG: er) på AzureFirewallSubnet?

Azure Firewall är en hanterad tjänst med flera skydds lager, inklusive plattforms skydd med NSG: er på NÄTVERKSKORTet (visas inte).  NSG: er för under näts nivå krävs inte på AzureFirewallSubnet och har inaktiverats för att säkerställa att inga avbrott uppstår i tjänsten.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur gör jag för att konfigurerar du Azure-brandväggen med mina tjänst slut punkter?

För säker åtkomst till PaaS-tjänster rekommenderar vi tjänst slut punkter. Du kan välja att aktivera tjänstens slut punkter i Azure Firewall-undernätet och inaktivera dem i anslutna virtuella eker-nätverk. På så sätt kan du dra nytta av båda funktionerna: tjänstens slut punkts säkerhet och Central loggning för all trafik.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Vad är prissättningen för Azure Firewall?

Se [priser för Azure-brandvägg](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hur kan jag stoppa och starta Azure-brandväggen?

Du kan använda Azure PowerShell *frigör* och *allokera* metoder.

Exempel:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Du måste omallokera en brand vägg och offentlig IP-adress till den ursprungliga resurs gruppen och prenumerationen.

## <a name="what-are-the-known-service-limits"></a>Vilka är de kända tjänst begränsningarna?

För Azure Firewall service-gränser, se [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure-brandväggen i ett virtuellt hubb nätverk vidarebefordra och filtrera nätverks trafik mellan två ekrar virtuella nätverk?

Ja, du kan använda Azure-brandväggen i ett nav virtuellt nätverk för att dirigera och filtrera trafik mellan två ekrar i ekrar. Undernät i varje eker virtuella nätverk måste ha en UDR som pekar på Azure-brandväggen som en standardgateway för att det här scenariot ska fungera korrekt.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure Firewall vidarebefordra och filtrera nätverks trafik mellan undernät i samma virtuella nätverk eller peer-kopplat virtuella nätverk?

Ja. Att konfigurera UDR för att omdirigera trafik mellan undernät i samma VNET kräver dock ytterligare uppmärksamhet. När du använder VNET-adressintervallet som ett målvärde för UDR, så dirigeras även all trafik från en dator till en annan dator i samma undernät via Azure Firewall-instansen. Undvik detta genom att inkludera en väg för under nätet i UDR med nästa hopp typ för **VNet**. Att hantera dessa vägar kan vara besvärligt och felkänsligt. Den rekommenderade metoden för intern nätverks segmentering är att använda nätverks säkerhets grupper, vilket inte kräver UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Har Azure Firewall utgående SNAT mellan privata nätverk?

Azure-brandväggen är inte SNAT när målets IP-adress är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Om din organisation använder ett offentligt IP-adressintervall för privata nätverk SNATs trafiken till någon av brand väggens privata IP-adresser i AzureFirewallSubnet med Azure-brandväggen. Du kan konfigurera Azure-brandväggen så att den **inte är** SNAT för ditt offentliga IP-adressintervall. Mer information finns i [Azure FIREWALL SNAT privata IP-adressintervall](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Stöds Tvingad tunnel trafik/länkning till en virtuell nätverks installation?

Tvingad tunnel trafik stöds när du skapar en ny brand vägg. Du kan inte konfigurera en befintlig brand vägg för Tvingad tunnel trafik. Mer information finns i [Tvingad tunnel trafik i Azure Firewall](forced-tunneling.md).

Azure Firewall måste ha direktanslutning till internet. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP måste du åsidosätta detta med en 0.0.0.0/0-UDR med **NextHopType** -värdet som **Internet** för att upprätthålla direkt Internet anslutning.

Om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk och du kan fastställa målets IP-prefix för dina Internet-destinationer, kan du konfigurera dessa intervall med det lokala nätverket som nästa hopp via en användardefinierad väg på AzureFirewallSubnet. Du kan också använda BGP för att definiera dessa vägar.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Finns det några begränsningar för brand Väggs resurs gruppen?

Ja. Brand väggen, VNet och den offentliga IP-adressen måste finnas i samma resurs grupp.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>När du konfigurerar DNAT för inkommande Internet nätverks trafik måste jag också konfigurera en motsvarande nätverks regel för att tillåta trafiken?

Nej. NAT-regler lägger implicit till en motsvarande nätverks regel för att tillåta översatta trafik. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hur fungerar jokertecken i en program regels mål-FQDN?

Jokertecken kan för närvarande endast användas på vänster sida av FQDN. Till exempel **_. contoso.com_* och **_contoso.com_*.

Om du konfigurerar **_. contoso.com_* tillåts *anyvalue*. contoso.com, men inte contoso.com (domän Apex). Om du vill tillåta domän Apex måste du uttryckligen konfigurera den som en mål-FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Vad är *etablerings status: misslyckades,* betyder?

När du gör en konfigurationsändring försöker Azure Firewall uppdatera alla underliggande serverdelsinstanser. I sällsynta fall kan en av dessa Server dels instanser Miss lyckas med att uppdatera med den nya konfigurationen och uppdaterings processen stoppas med ett misslyckat etablerings tillstånd. Din Azure Firewall-instans fungerar fortfarande, men den tillämpade konfigurationen kan vara i ett inkonsekvent tillstånd där vissa instanser har den tidigare konfigurationen där andra har den uppdaterade regeluppsättningen. Om detta inträffar kan du försöka att uppdatera konfigurationen en gång tills åtgärden lyckas och brand väggen har statusen *slutförd* etablering.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hur hanterar Azure Firewall planerat underhåll och oplanerade fel?
Azure-brandväggen består av flera backend-noder i en aktiv-aktiv-konfiguration.  För ett planerat underhåll har vi anslutning som tömmer logik för att på ett smidigt sätt uppdatera noder.  Uppdateringar planeras under icke kontors tid för var och en av Azure-regionerna för att ytterligare begränsa risken för avbrott.  Vid oplanerade problem instansierar vi en ny nod för att ersätta den felaktiga noden.  Anslutning till den nya noden återupprättas vanligt vis inom 10 sekunder från tidpunkten för det här problemet.

## <a name="how-does-connection-draining-work"></a>Hur tömmer sig anslutnings arbetet?

När det gäller planerat underhåll, uppdaterar den logiska logiken Server dels noderna. Azure-brandväggen väntar i 90 sekunder innan befintliga anslutningar stängs. Vid behov kan klienter automatiskt återupprätta anslutningen till en annan backend-nod.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Finns det en tecken gräns för ett brand Väggs namn?

Ja. Det finns en gräns på 50 tecken för ett brand Väggs namn.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Varför behöver Azure-brandväggen en/26-nätstorlek?

Azure-brandväggen måste etablera fler virtuella dator instanser när den skalas. Ett/26-adressutrymme säkerställer att brand väggen har tillräckligt med IP-adresser för att kunna hantera skalningen.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Måste brand Väggs under näts storleken ändras när tjänsten skalas?

Nej. Azure-brandväggen behöver inte ett undernät som är större än/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hur kan jag öka mitt brand Väggs flöde?

Den första data flödes kapaciteten i Azure Firewall är 2,5-3 Gbit/s och den skalas upp till 30 Gbit/s. Den skalas ut automatiskt baserat på CPU-användning och data flöde.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Hur lång tid tar det för Azure-brandväggen att skala ut?

Azure-brandväggen skalas gradvis när genomsnitts data flödet eller CPU-förbrukningen är 60%. Ett maximalt standard flöde för distribution är cirka 2,5-3 Gbit/s och börjar skala ut när det når 60% av det numret. Skala ut tar fem till sju minuter. 

När du testar prestanda kontrollerar du att du testar minst 10 till 15 minuter och startar nya anslutningar för att dra nytta av de nyligen skapade brand Väggs noderna.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Tillåter Azure-brandväggen åtkomst till Active Directory som standard?

Nej. Azure-brandväggen blockerar Active Directory åtkomst som standard. För att tillåta åtkomst konfigurerar du AzureActiveDirectory service tag. Mer information finns i [Azure Firewall service-Taggar](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Kan jag undanta ett fullständigt domän namn eller en IP-adress från Azure Firewall Threat Intelligence-filtrering?

Ja, du kan använda Azure PowerShell för att göra det:

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Varför kan en TCP-ping och liknande verktyg ansluta till ett mål-FQDN även när ingen regel på Azure-brandväggen tillåter trafik?

En TCP-ping ansluter faktiskt till mål-FQDN. Detta inträffar eftersom Azure firewalls transparenta proxy lyssnar på port 80/443 för utgående trafik. TCP-ping upprättar en anslutning till brand väggen, som sedan släpper paketet. Det här beteendet har ingen säkerhets påverkan. Men för att undvika förvirring undersöker vi eventuella ändringar i det här beteendet.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Finns det några begränsningar för hur många IP-adresser som stöds av IP-grupperna?

Ja. Mer information finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Kan jag flytta en IP-grupp till en annan resurs grupp?

Nej, det finns för närvarande inte stöd för att flytta en IP-grupp till en annan resurs grupp.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Vad är timeout för TCP-inaktivitet för Azure-brandväggen?

Ett standard beteende för en nätverks brand vägg är att se till att TCP-anslutningar hålls aktiva och att du snabbt ska stänga dem om det inte finns någon aktivitet. Timeout för TCP-inaktivitet i Azure Firewall är fyra minuter. Den här inställningen kan inte konfigureras. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP-eller HTTP-sessionen upprätthålls. En vanlig metod är att använda en TCP Keep-Alive. Den här metoden håller anslutningen aktiv under en längre period. Mer information finns i .net- [exemplen](/dotnet/api/system.net.servicepoint.settcpkeepalive?view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Kan jag Distribuera Azure-brandväggen utan en offentlig IP-adress?

Nej, för närvarande måste du Distribuera Azure-brandväggen med en offentlig IP-adress.

## <a name="where-does-azure-firewall-store-customer-data"></a>Var lagrar Azure Firewall kund information?

Azure-brandväggen flyttar eller lagrar inte kund information från den region som den har distribuerats i.