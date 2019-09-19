---
title: Vanliga frågor och svar om Azure-brandvägg
description: Vanliga frågor och svar om Azure-brandväggen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: cb5b8bbb322dc401c7a8b057418d392120ef68e3
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130213"
---
# <a name="azure-firewall-faq"></a>Vanliga frågor och svar om Azure-brandvägg

## <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Vilka funktioner stöds i Azure Firewall?

* Tillståndskänslig brandvägg som en tjänst
* Inbyggd hög tillgänglighet med obegränsad skalbarhet i molnet
* FQDN-filtrering
* Taggar för fullständigt domännamn
* Regler för filtrering av nätverkstrafik
* Stöd för utgående SNAT
* Stöd för inkommande DNAT
* Skapa, tillämpa och logga program-och nätverks anslutnings principer centralt i Azure-prenumerationer och virtuella nätverk
* Fullständig integrering med Azure Monitor för loggning och analys

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Vad är en typisk distributions modell för Azure-brandväggen?

Du kan distribuera Azure-brandväggen på alla virtuella nätverk, men kunderna distribuerar dem vanligt vis på ett centralt virtuellt nätverk och peer-andra virtuella nätverk till den i en nav-och-eker-modell. Du kan sedan ange standard vägen från de peer-peer-virtuella nätverken så att de pekar på det här centrala brand Väggs nätverket. Global VNet-peering stöds, men det rekommenderas inte på grund av eventuella problem med prestanda och latens i flera regioner. För bästa prestanda bör du distribuera en brand vägg per region.

Fördelen med den här modellen är möjligheten att centralt utöva kontroll över flera eker-virtuella nätverk över olika prenumerationer. Det kostar också besparingar eftersom du inte behöver distribuera en brand vägg i varje VNet separat. Kostnads besparingarna bör mätas jämfört med den kopplade peering-kostnaden baserat på kundens trafik mönster.

## <a name="how-can-i-install-the-azure-firewall"></a>Hur kan jag installera Azure-brandväggen?

Du kan konfigurera Azure-brandväggen med hjälp av Azure Portal, PowerShell, REST API eller med hjälp av mallar. Se [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp](tutorial-firewall-deploy-portal.md) av Azure Portal för stegvisa instruktioner.

## <a name="what-are-some-azure-firewall-concepts"></a>Vilka är några Azure Firewall-koncept?

Azure-brandväggen stöder regler och regel samlingar. En regel samling är en uppsättning regler som delar samma ordning och prioritet. Regel samlingar körs i prioritetsordning. Nätverks regel samlingar är högre prioritet än program regel samlingar och alla regler avslutas.

Det finns tre typer av regel samlingar:

* *Program regler*: Konfigurera fullständigt kvalificerade domän namn (FQDN) som kan nås från ett undernät.
* *Nätverks regler*: Konfigurera regler som innehåller käll adresser, protokoll, mål portar och mål adresser.
* *NAT-regler*: Konfigurera DNAT-regler för att tillåta inkommande anslutningar.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Stöder Azure-brandväggen inkommande trafik filtrering?

Azure-brandväggen stöder inkommande och utgående filtrering. Inkommande skydd är för protokoll som inte är HTTP/S. Till exempel RDP-, SSH-och FTP-protokoll.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Vilka loggnings-och analys tjänster stöds av Azure-brandväggen?

Azure-brandväggen är integrerad med Azure Monitor för att visa och analysera brand Väggs loggar. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hubs. De kan analyseras i Log Analytics eller av olika verktyg som Excel och Power BI. Mer information finns i [Självstudie: Övervaka Azure Firewall-](tutorial-diagnostics.md)loggar.

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hur fungerar Azure Firewall annorlunda jämfört med befintliga tjänster som NVA på Marketplace?

Azure Firewall är en grundläggande brand Väggs tjänst som kan hantera vissa kund scenarier. Det förväntas att du har en blandning av NVA och Azure-brandväggen från tredje part. Att arbeta bättre tillsammans är en grundläggande prioritet.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure-brandväggen?

Brand väggen för webbaserade program (WAF) är en funktion i Application Gateway som tillhandahåller centraliserat inkommande skydd av dina webb program från vanliga sårbarheter och sårbarheter. Azure-brandväggen ger inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP), utgående skydd på nätverks nivå för alla portar och protokoll och skydd på program nivå för utgående HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Vad är skillnaden mellan nätverks säkerhets grupper (NSG: er) och Azure-brandväggen?

Azure Firewall service kompletterar funktioner för nätverks säkerhets grupper. Tillsammans ger de bättre nätverks säkerhet i "försvar och djupgående". Nätverks säkerhets grupper tillhandahåller distribuerad trafik filtrering i nätverks skikt för att begränsa trafik till resurser i virtuella nätverk i varje prenumeration. Azure-brandväggen är en fullständigt tillstånds känslig, centraliserad nätverks brand vägg som en tjänst, som tillhandahåller skydd på Nätverks-och program nivå för olika prenumerationer och virtuella nätverk.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Stöds nätverks säkerhets grupper (NSG: er) i Azure Firewall-undernätet?

Azure Firewall är en hanterad tjänst med flera skydds lager, inklusive plattforms skydd med NSG: er på NÄTVERKSKORTet (visas inte).  NSG: er för under näts nivå krävs inte i Azure Firewall-undernätet och har inaktiverats för att säkerställa att inga avbrott uppstår i tjänsten.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur gör jag för att konfigurerar du Azure-brandväggen med mina tjänst slut punkter?

För säker åtkomst till PaaS-tjänster rekommenderar vi tjänst slut punkter. Du kan välja att aktivera tjänstens slut punkter i Azure Firewall-undernätet och inaktivera dem i anslutna virtuella eker-nätverk. På så sätt kan du dra nytta av båda funktionerna--tjänstens slut punkts säkerhet och centrala loggning för all trafik.

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
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Du måste omallokera en brand vägg och offentlig IP-adress till den ursprungliga resurs gruppen och prenumerationen.

## <a name="what-are-the-known-service-limits"></a>Vilka är de kända tjänst begränsningarna?

För Azure Firewall service-gränser, se [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure-brandväggen i ett virtuellt hubb nätverk vidarebefordra och filtrera nätverks trafik mellan två ekrar virtuella nätverk?

Ja, du kan använda Azure-brandväggen i ett nav virtuellt nätverk för att dirigera och filtrera trafik mellan två ekrar i ekrar. Undernät i varje Ekers virtuella nätverk måste ha UDR som pekar på Azure-brandväggen som standard-gateway för att det här scenariot ska fungera korrekt.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure Firewall vidarebefordra och filtrera nätverks trafik mellan undernät i samma virtuella nätverk eller peer-kopplat virtuella nätverk?

Ja. Att konfigurera UDR för att omdirigera trafik mellan undernät i samma VNET kräver dock ytterligare uppmärksamhet. När du använder VNET-adressintervallet som ett målvärde för UDR, så dirigeras även all trafik från en dator till en annan dator i samma undernät via Azure Firewall-instansen. Undvik detta genom att inkludera en väg för under nätet i UDR med nästa hopp typ för **VNet**. Att hantera dessa vägar kan vara besvärligt och felkänsligt. Den rekommenderade metoden för intern nätverks segmentering är att använda nätverks säkerhets grupper, vilket inte kräver UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Har Azure Firewall utgående SNAT mellan privata nätverk?

Azure-brandväggen är inte SNAT när målets IP-adress är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Om din organisation använder ett offentligt IP-adressintervall för privata nätverk SNATs trafiken till någon av brand väggens privata IP-adresser i AzureFirewallSubnet med Azure-brandväggen.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Stöds Tvingad tunnel trafik/länkning till en virtuell nätverks installation?

Tvingad tunnel trafik stöds inte för närvarande. Azure-brandväggen måste ha direkt Internet anslutning. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP måste du åsidosätta detta med en 0.0.0.0/0-UDR med **NextHopType** -värdet som **Internet** för att upprätthålla direkt Internet anslutning.

Om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk och du kan fastställa målets IP-prefix för dina Internet-destinationer, kan du konfigurera dessa intervall med det lokala nätverket som nästa hopp via en användardefinierad väg på AzureFirewallSubnet. Du kan också använda BGP för att definiera dessa vägar.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Finns det några begränsningar för brand Väggs resurs gruppen?

Ja. Brand väggen, under nätet, VNet och den offentliga IP-adressen måste finnas i samma resurs grupp.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>När du konfigurerar DNAT för inkommande nätverks trafik måste du också konfigurera en motsvarande nätverks regel för att tillåta trafiken?

Nej. NAT-regler lägger implicit till en motsvarande nätverks regel för att tillåta översatta trafik. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hur fungerar jokertecken i en program regels mål-FQDN?

Om du konfigurerar * **. contoso.com**tillåts *anyvalue*. contoso.com, men inte contoso.com (domän Apex). Om du vill tillåta domän Apex måste du uttryckligen konfigurera den som en mål-FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Vad innebär *etablerings status: Misslyckades* betyder?

När en konfigurations ändring används försöker Azure-brandväggen uppdatera alla underliggande Server dels instanser. I sällsynta fall kan en av dessa Server dels instanser Miss lyckas med att uppdatera med den nya konfigurationen och uppdaterings processen stoppas med ett misslyckat etablerings tillstånd. Din Azure-brandvägg fungerar fortfarande, men den tillämpade konfigurationen kan vara i ett inkonsekvent tillstånd, där vissa instanser har den tidigare konfigurationen där andra har den uppdaterade regel uppsättningen. Om detta inträffar kan du försöka att uppdatera konfigurationen en gång tills åtgärden lyckas och brand väggen har statusen *slutförd* etablering.

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hur hanterar Azure Firewall planerat underhåll och oplanerade fel?
Azure-brandväggen består av flera backend-noder i en aktiv-aktiv-konfiguration.  För ett planerat underhåll har vi anslutning som tömmer logik för att på ett smidigt sätt uppdatera noder.  Uppdateringar planeras under icke kontors tid för var och en av Azure-regionerna för att ytterligare begränsa risken för avbrott.  Vid oplanerade problem instansierar vi en ny nod för att ersätta den felaktiga noden.  Anslutning till den nya noden återupprättas vanligt vis inom 10 sekunder från tidpunkten för det här problemet.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Finns det en tecken gräns för ett brand Väggs namn?

Ja. Det finns en gräns på 50 tecken för ett brand Väggs namn.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Varför behöver Azure-brandväggen en/26-nätstorlek?

Azure-brandväggen måste etablera fler virtuella dator instanser när den skalas. Ett/26-adressutrymme säkerställer att brand väggen har tillräckligt med IP-adresser för att kunna hantera skalningen.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Måste brand Väggs under näts storleken ändras när tjänsten skalas?

Nej. Azure-brandväggen behöver inte ett undernät som är större än/26.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Tillåter Azure-brandväggen åtkomst till Active Directory som standard?

Nej. Azure-brandväggen blockerar Active Directory åtkomst som standard. För att tillåta åtkomst konfigurerar du AzureActiveDirectory service tag. Mer information finns i [Azure Firewall service-Taggar](service-tags.md).
