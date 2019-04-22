---
title: Vanliga frågor om Azure-brandväggen
description: Vanliga frågor och svar för Azure-brandväggen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: a89a7e3abbb94465a2ea5eb61eda3e967c26d1f0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680834"
---
# <a name="azure-firewall-faq"></a>Vanliga frågor om Azure-brandväggen

## <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt administrerad brandvägg-som-tjänst-med inbyggd hög tillgänglighet och skalbarhet i obegränsad molnet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Vilka funktioner stöds i Azure-brandvägg?

* Tillståndskänslig brandvägg som en tjänst
* Inbyggd hög tillgänglighet med obegränsad skalbarhet i molnet
* FQDN-filtrering
* FQDN-taggar
* Regler för filtrering av nätverkstrafik
* Stöd för utgående SNAT
* Stöd för inkommande DNAT
* Skapa, tillämpa och logga principer för program och nätverk-anslutning via Azure-prenumerationer och virtuella nätverk centralt
* Fullständig integrering med Azure Monitor för loggning och analys

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Vad är den typiska distributionsmodellen för Azure-brandvägg?

Du kan distribuera Azure-brandväggen på alla virtuella nätverk, men kunder normalt distribuerar den på en central virtuellt nätverk och peer-andra virtuella nätverk till den i en nav-och-eker-modell. Du kan sedan ange standardvägen från peer-kopplade virtuella nätverk så att den pekar till den här virtuella nätverket centrala brandväggen. Global VNet-peering stöds, men det rekommenderas inte på grund av potentiella prestanda och svarstidsproblem i olika regioner. Distribuera en brandvägg per region för bästa prestanda.

Fördelen med den här modellen är möjligheten att centralt utöva kontrollen i flera ekrar virtuella nätverk i olika prenumerationer. Det finns även kostnadsbesparingar som du inte behöver distribuera en brandvägg i varje virtuellt nätverk separat. Besparingarna mäts och koppla peering kostnad som baseras på trafikmönster kund.

## <a name="how-can-i-install-the-azure-firewall"></a>Hur kan jag installera Azure-brandvägg?

Du kan ställa in Azure-brandvägg med hjälp av Azure portal, PowerShell, REST API, eller med hjälp av mallar. Se [självstudien: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](/articles/firewall/tutorial-firewall-deploy-portal.md) stegvisa instruktioner.

## <a name="what-are-some-azure-firewall-concepts"></a>Vilka är några begrepp för Azure-brandvägg?

Azure-brandväggen har stöd för regler och regelsamlingar. En regelsamling är en uppsättning regler som delar samma ordning och prioritet. Regelsamlingar utförs efter deras prioritet. Nätverket regelsamlingar har högre prioritet än program regelsamlingar och avslutas på alla regler.

Det finns tre typer av samlingar:

* *Regler för program*: Konfigurera fullständigt kvalificerade domännamn (FQDN) som kan nås från ett undernät.
* *Network regler*: Konfigurera regler som innehåller källadresser, protokoll, målportar och måladresser.
* *NAT-regler*: Konfigurera DNAT regler för att tillåta inkommande anslutningar.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Stöder Azure Brandvägg för inkommande trafikfiltrering?

Azure-brandväggen har stöd för filtrering av inkommande och utgående. Inkommande skydd är för icke-HTTP/S-protokoll. Till exempel RDP, SSH och FTP-protokoll.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Vilka loggning och Analystjänster stöds av Azure-brandvägg?

Azure-brandväggen är integrerad med Azure Monitor för att visa och analysera loggar från brandväggen. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hubs. De kan analyseras i Log Analytics eller genom att olika verktyg som Excel och Power BI. Mer information finns i [Självstudie: Övervaka Azure brandväggsloggar](/articles/firewall/tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hur fungerar Azure-brandväggen på olika sätt från befintliga tjänster, till exempel nva: er på marketplace?

Brandvägg för Azure är en enkel brandvägg-tjänst som kan lösa vissa kundscenarier med. Det är normalt att du har en blandning av tredjeparts-nva: er och Azure-brandvägg. Fungerar bättre tillsammans är prioriterat core.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure-brandvägg?

Web Application Firewall (WAF) är en funktion i Application Gateway som ger ett centraliserat inkommande skydd av dina webbappar mot vanliga kryphål och säkerhetsproblem. Azure-brandväggen ger inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP) och utgående på nätverksnivå skydd för alla portar och protokoll på programnivå skydd för utgående HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Vad är skillnaden mellan Nätverkssäkerhetsgrupper (NSG) och Azure-brandvägg?

Azure brandväggstjänsten kompletterar network security group funktioner. Tillsammans kan de ger bättre ”skydd på djupet” nätverkssäkerhet. Nätverkssäkerhetsgrupper innehåller distribuerade layer filtrering av nätverkstrafik för att begränsa trafik till resurser i virtuella nätverk i varje prenumeration. Azure-brandväggen är en fullständigt administrerad, centraliserad nätverk brandväggen som-tjänst, vilket ger nätverks - och programnivå skydd i olika prenumerationer och virtuella nätverk.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur ställer jag in Azure-brandvägg med min tjänstslutpunkter?

Vi rekommenderar tjänstslutpunkter för säker åtkomst till PaaS-tjänster. Du kan välja att aktivera Tjänsteslutpunkter i Azure-brandvägg undernät och inaktivera dem på anslutna virtuella ekernätverk. Det här sättet du dra nytta av funktioner – tjänsten endpoint säkerhets- och centrala loggning för all trafik.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Vad är priset för Azure-brandvägg?

Se [priser för Azure-brandväggen](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hur kan jag för att stoppa och starta Azure brandvägg?

Du kan använda Azure PowerShell *Frigör* och *allokera* metoder.

Exempel:

```azurepowershell
# Stop an exisitng firewall

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
> Du måste omfördela en brandvägg och en offentlig IP-adress till den ursprungliga resursgruppen och prenumerationen.

## <a name="what-are-the-known-service-limits"></a>Vad kända gränser har tjänsten?

Tjänstbegränsningar för Azure-brandväggen, se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](/articles/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure-brandvägg i en virtuella navnätverket framåt och filtrera nätverkstrafik mellan två virtuella ekernätverk?

Ja, du kan använda Azure-brandvägg i en virtuella navnätverket kan dirigera och filtrera trafik mellan två eker-nätverk. Undernät i varje eker virtuella nätverk måste ha UDR som pekar på Azure-brandväggen som en standard-gateway för det här scenariot ska fungera korrekt.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure-brandvägg framåt och filtrera nätverkstrafik mellan undernät i samma virtuella nätverk eller peerkopplade virtuella nätverk?

Ja. Konfigurera udr: er för att omdirigera trafik mellan undernät i samma virtuella nätverk kräver dock ytterligare uppmärksamhet. När du använder VNET-adressintervall som ett mål-prefix för den användardefinierade vägen är tillräcklig kan dirigerar detta även all trafik från en dator till en annan dator i samma undernät genom brandväggen för Azure-instans. Undvik detta genom att inkludera en väg för undernätet i den användardefinierade vägen med ett nexthop-typ för **VNET**. Hantera dessa vägar kan vara besvärligt och felbenägna. Den rekommenderade metoden för interna nätverkssegmentering är att använda Nätverkssäkerhetsgrupper som inte kräver udr: er.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Är framtvingad tunneling/länkning till en virtuell nätverksinstallation som stöds?

Tvingad tunneltrafik stöds inte som standard, men den kan aktiveras med hjälp av Support.

Azure-brandväggen måste ha direkt Internetanslutning. AzureFirewallSubnet har som standard en väg med 0.0.0.0/0 med NextHopType värdet **Internet**.

Om du aktiverar Tvingad tunneltrafik till lokalt via ExpressRoute eller VPN-Gateway, kan du behöva uttryckligen konfigurera en 0.0.0.0/0 användardefinierad väg (UDR) med NextHopType värde som Internet och associera den med din AzureFirewallSubnet. Detta åsidosätter eventuella standard-gateway BGP-meddelandet tillbaka till ditt lokala nätverk.

Om din organisation kräver Tvingad tunneltrafik för Azure-brandvägg att dirigera standard gateway trafiken tillbaka via ditt lokala nätverk, kontakta supporten. Vi kan godkänna din prenumeration för att se till att nödvändiga brandväggen Internetanslutning upprätthålls.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Finns det någon brandvägg resource group begränsningar?

Ja. Brandväggen, undernät, virtuella nätverk och den offentliga IP-adressen måste vara i samma resursgrupp.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>När du konfigurerar DNAT för inkommande nätverkstrafik, också behöver jag konfigurera en motsvarande regel för att tillåta den trafiken?

Nej. NAT-regler är implicit lägga till en regel för motsvarande för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](/articles/firewall/rule-processing.md).

## <a name="how-to-wildcards-work-in-an-application-rule-target-fqdn"></a>Hur till jokertecken fungerar i ett program regelmål FQDN?

Om du konfigurerar ***. contoso.com**, tillåter *anyvalue*. contoso.com, men inte contoso.com (domän överst). Om du vill tillåta överst domän måste du uttryckligen konfigurera den som ett FQDN-mål.

## <a name="what-does-provisioning-state-failed-mean"></a>Vad gör *Etableringsstatus: Det gick inte* betyder?

När en konfigurationsändring tillämpas, försöker Azure brandvägg att uppdatera dess underliggande serverdelsinstanser. I sällsynta fall kan en av dessa serverdelsinstanser kanske inte kan uppdatera med den nya konfigurationen och uppdateringsprocessen slutar med ett felaktigt tillstånd för etablering. Din brandvägg för Azure fungerar fortfarande, men tillämpad konfiguration kan vara i ett inkonsekvent tillstånd, där vissa fall har den tidigare konfigurationen där andra har den uppdaterade regeluppsättningen. Om det händer, försök att uppdatera konfigurationen en gång, tills åtgärden lyckas och brandväggen finns i en *lyckades* Etableringsstatus.
