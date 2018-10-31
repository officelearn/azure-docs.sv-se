---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 10/20/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: e33871f35613fbd5cdc5bf3162855b942056807f
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254730"
---
### <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt administrerad brandvägg-som-tjänst-med inbyggd hög tillgänglighet och skalbarhet i obegränsad molnet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Vilka funktioner stöds i Azure-brandvägg?

* Tillståndskänslig brandvägg som en tjänst
* Inbyggd hög tillgänglighet med obegränsad skalbarhet i molnet
* FQDN-filtrering
* FQDN-taggar
* Regler för filtrering av nätverkstrafik
* Stöd för utgående SNAT
* Stöd för inkommande DNAT
* Skapa, tillämpa och logga principer för program och nätverk-anslutning via Azure-prenumerationer och virtuella nätverk centralt
* Fullständig integrering med Azure Monitor för loggning och analys

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Vad är den typiska distributionsmodellen för Azure-brandvägg?

Du kan distribuera Azure-brandväggen på alla virtuella nätverk, men kunder normalt distribuerar den på en central virtuellt nätverk och peer-andra virtuella nätverk till den i en nav-och-eker-modell. Du kan sedan ange standardvägen från peer-kopplade virtuella nätverk så att den pekar till den här virtuella nätverket centrala brandväggen.

### <a name="how-can-i-install-the-azure-firewall"></a>Hur kan jag installera Azure-brandvägg?

Du kan ställa in Azure-brandvägg med hjälp av Azure portal, PowerShell, REST API, eller med hjälp av mallar. Se [självstudie: distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](../articles/firewall/tutorial-firewall-deploy-portal.md) stegvisa instruktioner.

### <a name="what-are-some-azure-firewall-concepts"></a>Vilka är några begrepp för Azure-brandvägg?

Azure-brandväggen har stöd för regler och regelsamlingar. En regelsamling är en uppsättning regler som delar samma ordning och prioritet. Regelsamlingar utförs efter deras prioritet. Nätverket regelsamlingar har högre prioritet än program regelsamlingar och avslutas på alla regler.

Det finns två typer av samlingar:

* *Regler för program*: du kan konfigurera fullständigt kvalificerade domännamn (FQDN) som kan nås från ett undernät.
* *Network regler*: du kan konfigurera regler som innehåller källadresser, protokoll, målportar och måladresser.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Stöder Azure Brandvägg för inkommande trafikfiltrering?

Azure-brandväggen har stöd för filtrering av inkommande och utgående. Inkommande skydd är för icke-HTTP/S-protokoll. Till exempel RDP, SSH och FTP-protokoll.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Vilka loggning och Analystjänster stöds av Azure-brandvägg?

Azure-brandväggen är integrerad med Azure Monitor för att visa och analysera loggar från brandväggen. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hubs. De kan analyseras i Log Analytics eller genom att olika verktyg som Excel och Power BI. Mer information finns i [självstudie: övervaka Azure brandväggsloggar](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hur fungerar Azure-brandväggen på olika sätt från befintliga tjänster, till exempel nva: er på marketplace?

Brandvägg för Azure är en enkel brandvägg-tjänst som kan lösa vissa kundscenarier med. Det är normalt att du har en blandning av tredjeparts-nva: er och Azure-brandvägg. Fungerar bättre tillsammans är prioriterat core.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure-brandvägg?

Web Application Firewall (WAF) är en funktion i Application Gateway som ger ett centraliserat inkommande skydd av dina webbappar mot vanliga kryphål och säkerhetsproblem. Azure-brandväggen ger inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP) och utgående på nätverksnivå skydd för alla portar och protokoll på programnivå skydd för utgående HTTP/S.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Vad är skillnaden mellan Nätverkssäkerhetsgrupper (NSG) och Azure-brandvägg?

Azure brandväggstjänsten kompletterar network security group funktioner. Tillsammans kan de ger bättre ”skydd på djupet” nätverkssäkerhet. Nätverkssäkerhetsgrupper innehåller distribuerade layer filtrering av nätverkstrafik för att begränsa trafik till resurser i virtuella nätverk i varje prenumeration. Azure-brandväggen är en fullständigt administrerad, centraliserad nätverk brandväggen som-tjänst, vilket ger nätverks - och programnivå skydd i olika prenumerationer och virtuella nätverk.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur ställer jag in Azure-brandvägg med min tjänstslutpunkter?

Vi rekommenderar tjänstslutpunkter för säker åtkomst till PaaS-tjänster. Du kan välja att aktivera Tjänsteslutpunkter i Azure-brandvägg undernät och inaktivera dem på anslutna virtuella ekernätverk. Det här sättet du dra nytta av funktioner – tjänsten endpoint säkerhets- och centrala loggning för all trafik.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Vad är priset för Azure-brandvägg?

Azure-brandväggen har en fast kostnad + rörlig kostnad:

* Fast avgift: $1.25/firewall/hour
* Varierande avgift: $0.03/ GB som behandlas av brandväggen (inkommande eller utgående)

Det finns inga kostnader för en frigjord brandvägg.

Mer information finns i [priser för Azure-brandväggen](https://azure.microsoft.com/pricing/details/azure-firewall/).

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Hur kan jag för att stoppa och starta Azure brandvägg?

Du kan använda Azure PowerShell *Frigör* och *allokera* metoder.

Exempel:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Du måste omfördela en brandvägg och en offentlig IP-adress till den ursprungliga resursgruppen och prenumerationen.

### <a name="what-are-the-known-service-limits"></a>Vad kända gränser har tjänsten?

Tjänstbegränsningar för Azure-brandväggen, se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure-brandvägg i en virtuella navnätverket framåt och filtrera nätverkstrafik mellan två virtuella ekernätverk?

Ja, du kan använda Azure-brandvägg i en virtuella navnätverket kan dirigera och filtrera trafik mellan två eker-nätverk. Undernät i varje eker virtuella nätverk måste ha UDR som pekar på Azure-brandväggen som en standard-gateway för det här scenariot ska fungera korrekt.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network"></a>Kan Azure-brandvägg framåt och filtrera nätverkstrafik mellan undernät i samma virtuella nätverk?

Trafik mellan undernät i samma virtuella nätverk eller i en direkt peerkopplade virtuella nätverk dirigeras direkt även om UDR pekar på Azure-brandväggen som standard-gateway. Den rekommenderade metoden för interna nätverkssegmentering är att använda Nätverkssäkerhetsgrupper. Om du vill skicka undernätet till undernätet trafik i brandväggen i det här scenariot, måste UDR innehålla mål nätverk undernätsprefixet uttryckligen på båda undernäten.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Finns det någon brandvägg resource group begränsningar?

Ja. Brandväggen, undernät, virtuella nätverk och den offentliga IP-adressen måste vara i samma resursgrupp.
