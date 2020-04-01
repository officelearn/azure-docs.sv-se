---
title: Vanliga frågor och svar om Azure-brandväggen
description: Vanliga frågor och svar om Azure-brandväggen. En hanterad, molnbaserad nätverkssäkerhetstjänst som skyddar dina Azure Virtual Network-resurser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 45276884d59ac8d1d876e2225ac02bb51c3f74fc
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437731"
---
# <a name="azure-firewall-faq"></a>Vanliga frågor och svar om Azure-brandväggen

## <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Vilka funktioner stöds i Azure-brandväggen?

* Tillståndskänslig brandvägg som en tjänst
* Inbyggd hög tillgänglighet med obegränsad molnskalbarhet
* FQDN-filtrering
* FQDN-taggar
* Regler för filtrering av nätverkstrafik
* Stöd för utgående SNAT
* Stöd för inkommande DNAT
* Centralt skapa, framtvinga och logga program- och nätverksanslutningsprinciper över Azure-prenumerationer och virtuella nätverk
* Helt integrerad med Azure Monitor för loggning och analys

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Vilken är den typiska distributionsmodellen för Azure-brandväggen?

Du kan distribuera Azure-brandväggen i alla virtuella nätverk, men kunder distribuerar den vanligtvis i ett centralt virtuellt nätverk och peer andra virtuella nätverk till den i en hub-and-spoke-modell. Du kan sedan ange att standardvägen från de peer-in-virtuella nätverken ska peka på det här virtuella nätverket för den centrala brandväggen. Global VNet-peering stöds, men det rekommenderas inte på grund av potentiella prestanda- och svarstidsproblem i olika regioner. För bästa prestanda kan du distribuera en brandvägg per region.

Fördelen med denna modell är möjligheten att centralt utöva kontroll på flera eker VNETs över olika prenumerationer. Det finns också kostnadsbesparingar eftersom du inte behöver distribuera en brandvägg i varje virtuella nätverk separat. Kostnadsbesparingarna ska mätas jämfört med den associerade peering-kostnaden baserat på kundens trafikmönster.

## <a name="how-can-i-install-the-azure-firewall"></a>Hur installerar jag Azure-brandväggen?

Du kan konfigurera Azure-brandväggen med hjälp av Azure-portalen, PowerShell, REST API eller genom att använda mallar. Se [självstudiekurs: Distribuera och konfigurera Azure-brandväggen med Hjälp av Azure-portalen](tutorial-firewall-deploy-portal.md) för steg-för-steg-instruktioner.

## <a name="what-are-some-azure-firewall-concepts"></a>Vilka är några Azure-brandväggsbegrepp?

Azure Firewall stöder regler och regelsamlingar. En regelsamling är en uppsättning regler som har samma ordning och prioritet. Regelsamlingar körs i prioritetsordning. Nätverksregelsamlingar har högre prioritet än programregelsamlingar och alla regler avslutas.

Det finns tre typer av regelsamlingar:

* *Programregler*: Konfigurera fullständigt kvalificerade domännamn (FQDN) som kan nås från ett undernät.
* *Nätverksregler*: Konfigurera regler som innehåller källadresser, protokoll, målportar och måladresser.
* *NAT-regler*: Konfigurera DNAT-regler så att inkommande Internet-anslutningar tillåts.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Stöder Azure-brandväggen filtrering av inkommande trafik?

Azure-brandväggen stöder inkommande och utgående filtrering. Inkommande skydd används vanligtvis för protokoll som inte är HTTP/S. Till exempel RDP-, SSH- och FTP-protokoll. Bäst inkommande HTTP/S-skydd används för bästa HTTP/S-skydd genom att använda en brandvägg för webbprogram, till exempel [Azure Web Application Firewall (WAF).](../web-application-firewall/overview.md)

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Vilka loggnings- och analystjänster stöds av Azure-brandväggen?

Azure-brandväggen är integrerad med Azure Monitor för visning och analys av brandväggsloggar. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hubs. De kan analyseras i Log Analytics eller med olika verktyg som Excel och Power BI. Mer information finns i [Självstudiekurs: Övervaka Azure-brandväggsloggar](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hur fungerar Azure Firewall annorlunda än befintliga tjänster som NVA:er på marknadsplatsen?

Azure Firewall är en grundläggande brandväggstjänst som kan hantera vissa kundscenarier. Det förväntas att du har en blandning av NVA:er från tredje part och Azure-brandväggen. Att arbeta bättre tillsammans är en grundläggande prioritet.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure Firewall?

Waf (Web Application Firewall) är en funktion i Application Gateway som ger centraliserat inkommande skydd av dina webbprogram från vanliga kryphål och sårbarheter. Azure-brandväggen ger inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP), utgående skydd på nätverksnivå för alla portar och protokoll och skydd på programnivå för utgående HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Vad är skillnaden mellan NSG och Azure-brandväggen?

Azure Firewall-tjänsten kompletterar nätverkssäkerhetsgruppfunktioner. Tillsammans ger de bättre "försvar på djupet" nätverkssäkerhet. Nätverkssäkerhetsgrupper tillhandahåller distribuerad trafikfiltrering av nätverkslager för att begränsa trafiken till resurser inom virtuella nätverk i varje prenumeration. Azure Firewall är en helt tillståndskänslig, centraliserad nätverksbrandvägg som en tjänst, som ger skydd på nätverks- och programnivå för olika prenumerationer och virtuella nätverk.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Stöds NSG (Network Security Groups) i undernätet för Azure-brandväggen?

Azure Firewall är en hanterad tjänst med flera skyddslager, inklusive plattformsskydd med NSG-kort på NIC-nivå (kan inte visas).  NSG:er på undernät på undernät på undernät för undernät på azure-brandväggen och inaktiveras för att säkerställa inget avbrott i tjänsten.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur konfigurerar jag Azure-brandväggen med mina tjänstslutpunkter?

För säker åtkomst till PaaS-tjänster rekommenderar vi tjänstslutpunkter. Du kan välja att aktivera tjänstslutpunkter i undernätet för Azure-brandväggen och inaktivera dem i de anslutna ekrarna virtuella nätverk. På så sätt kan du dra nytta av båda funktionerna: tjänstslutpunktssäkerhet och central loggning för all trafik.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Vad är prissättningen för Azure-brandväggen?

Se [Prissättningen för Azure-brandväggen](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hur stoppar och startar jag Azure Firewall?

Du kan använda Azure *PowerShell-deallocate* och *allokera* metoder.

Ett exempel:

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
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Du måste omfördela en brandvägg och offentlig IP till den ursprungliga resursgruppen och prenumerationen.

## <a name="what-are-the-known-service-limits"></a>Vilka är de kända servicegränserna?

Information om Azure-brandväggstjänstgränser finns i [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure-brandväggen i ett virtuellt nav-nätverk vidarebefordra och filtrera nätverkstrafik mellan två ekriska virtuella nätverk?

Ja, du kan använda Azure-brandväggen i ett virtuellt navnätverk för att dirigera och filtrera trafik mellan två ekrar virtuella nätverk. Undernät i vart och ett av de ekriska virtuella nätverken måste ha en UDR som pekar på Azure-brandväggen som en standardgateway för att det här scenariot ska fungera korrekt.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure Firewall vidarebefordra och filtrera nätverkstrafik mellan undernät i samma virtuella nätverk eller peer-virtuella nätverk?

Ja. Att konfigurera UDR:erna för att omdirigera trafik mellan undernät i samma virtuella nätverk kräver dock ytterligare uppmärksamhet. När du använder VNET-adressintervallet som ett målprefix för UDR är tillräckligt, dirigerar detta också all trafik från en dator till en annan dator i samma undernät via Azure Firewall-instansen. Undvik detta genom att inkludera en väg för undernätet i UDR med nästa hopptyp **av VNET**. Hantera dessa vägar kan vara besvärligt och benägna att fel. Den rekommenderade metoden för intern nätverkssegmentering är att använda nätverkssäkerhetsgrupper, som inte kräver UDRs.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Utgående SNAT-SNAT för Azure-brandväggen mellan privata nätverk?

Azure-brandväggen snat inte när målet IP-adressen är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Om din organisation använder ett offentligt IP-adressintervall för privata nätverk, Azure Firewall SNATs trafiken till en av brandväggen privata IP-adresser i AzureFirewallSubnet. Du kan konfigurera Azure-brandväggen så att **den inte** är ditt offentliga IP-adressintervall. Mer information finns i [Azure Firewall SNAT privata IP-adressintervall](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Stöds påtvingad tunnelning/kedja till en virtuell nätverksinstallation?

Påtvingad tunnelning stöds. Mer information finns i [Azure Firewall forced tunneling (preview)](forced-tunneling.md). 

Azure-brandväggen måste ha direkt Internet-anslutning. Om din AzureFirewallSubnet lär sig en standardväg till ditt lokala nätverk via BGP måste du åsidosätta detta med en UDR 0.0.0/0 med **NextHopType-värdet** som **Internet** för att upprätthålla direkt Internet-anslutning.

Om din konfiguration kräver tvångstunnelering till ett lokalt nätverk och du kan bestämma mål-IP-prefixen för dina Internet-destinationer, kan du konfigurera dessa intervall med det lokala nätverket som nästa hopp via en användardefinierad väg på AzureFirewallSubnet. Du kan också använda BGP för att definiera dessa vägar.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Finns det några begränsningar för brandväggsresurser?

Ja. Brandväggen, virtuella nätverk och den offentliga IP-adressen måste alla finnas i samma resursgrupp.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>När konfigurera DNAT för inkommande Internet-nätverkstrafik, måste jag också konfigurera en motsvarande nätverksregel för att tillåta att trafik?

Nej. NAT-regler lägger implicit till en motsvarande nätverksregel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hur fungerar jokertecken i en programregel mål FQDN?

Om du konfigurerar ***.contoso.com**tillåter det *alla värden*.contoso.com, men inte contoso.com (domänen apex). Om du vill tillåta domänapex måste du uttryckligen konfigurera den som ett mål FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Vad betyder *etableringstillstånd: Misslyckad?*

När en konfigurationsändring tillämpas försöker Azure-brandväggen uppdatera alla dess underliggande serverliggande instanser. I sällsynta fall kan en av dessa serverningsinstanser misslyckas med att uppdatera med den nya konfigurationen och uppdateringsprocessen slutar med ett misslyckat etableringstillstånd. Din Azure-brandvägg är fortfarande i drift, men den tillämpade konfigurationen kan vara i ett inkonsekvent tillstånd, där vissa instanser har den tidigare konfigurationen där andra har den uppdaterade regeluppsättningen. Om detta inträffar kan du prova att uppdatera konfigurationen en gång tills åtgärden lyckas och brandväggen är i ett *efterföljande etableringstillstånd.*

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hur hanterar Azure Firewall planerade underhålls- och oplanerade fel?
Azure-brandväggen består av flera serverdnoder i en aktiv-aktiv konfiguration.  För alla planerade underhåll har vi anslutningsdränningslogik för att smidigt uppdatera noder.  Uppdateringar planeras under icke-kontorstid för var och en av Azure-regionerna för att ytterligare begränsa risken för avbrott.  För oplanerade problem instansierar vi en ny nod för att ersätta den misslyckade noden.  Anslutningen till den nya noden återupprättas vanligtvis inom 10 sekunder från tidpunkten för felet.

## <a name="how-does-connection-draining-work"></a>Hur fungerar anslutningsdränning?

För planerat underhåll uppdaterar anslutningsdränningslogik graciöst backend-noder. Azure-brandväggen väntar 90 sekunder innan befintliga anslutningar stängs. Om det behövs kan klienter automatiskt återupprätta anslutningen till en annan serverdelsnod.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Finns det en teckengräns för ett brandväggsnamn?

Ja. Det finns en gräns på 50 tecken för ett brandväggsnamn.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Varför behöver Azure Firewall storleken på ett /26-undernät?

Azure-brandväggen måste etablera fler instanser av virtuella datorer när den skalas. Ett /26-adressutrymme säkerställer att brandväggen har tillräckligt med IP-adresser tillgängliga för skalningen.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Behöver brandväggens undernätsstorlek ändras när tjänsten skalas?

Nej. Azure-brandväggen behöver inte ett undernät som är större än /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hur kan jag öka mitt brandväggsdataflöde?

Azure Firewall ursprungliga dataflödeskapacitet är 2,5 - 3 Gbit/s och skalas ut till 30 Gbit/s. Den skalas ut baserat på CPU-användning och dataflöde. Kontakta supporten för att öka brandväggens dataflödeskapacitet.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Hur lång tid tar det för Azure-brandväggen att skala ut?

Det tar från fem till sju minuter för Azure Firewall att skala ut. Kontakta supporten för att öka brandväggens initiala dataflödeskapacitet om du har skurar som kräver en snabbare automatisk skalning.

Följande punkter bör beaktas när du testar brandväggen automatisk skalning:

- Prestanda för ett enda TCP-flöde är begränsat till 1,4 Gbit/s. Därför måste ett prestandatest upprätta flera TCP-flöden.
- Prestandaverktyg måste kontinuerligt upprätta nya anslutningar för att de ska kunna ansluta till de uppskalade serverda brandväggsinstanserna. Om testet upprättar anslutningar en gång i början, kommer de bara att ansluta till de första serverdinstanserna. Även om brandväggen skalas upp ser du ingen ökad prestanda eftersom anslutningarna är associerade med de första instanserna.


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Tillåter Azure-brandväggen åtkomst till Active Directory som standard?

Nej. Azure-brandväggen blockerar Active Directory-åtkomst som standard. Konfigurera servicetaggen AzureActiveDirectory om du vill tillåta åtkomst. Mer information finns i [Azure Firewall service tags](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Kan jag utesluta en FQDN eller en IP-adress från Azure Firewall Threat Intelligence-baserad filtrering?

Ja, du kan använda Azure PowerShell för att göra det:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Varför kan en TCP-ping och liknande verktyg ansluta till ett mål FQDN även när ingen regel på Azure Firewall tillåter den trafiken?

En TCP-ping ansluter faktiskt inte till målet FQDN. Detta beror på att Azure Firewall transparent proxy lyssnar på port 80/443 för utgående trafik. TCP-ping upprättar en anslutning till brandväggen, som sedan släpper paketet och loggar anslutningen. Det här beteendet har ingen säkerhetspåverkan. Men för att undvika förvirring undersöker vi potentiella ändringar av detta beteende. 