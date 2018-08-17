---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a63a12658bd0a4b4d018d51824af9814691a3cbf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "40183249"
---
### <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt administrerad brandvägg-som-tjänst-med inbyggd hög tillgänglighet och skalbarhet i obegränsad molnet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure-brandväggen är för närvarande i offentlig förhandsversion.

### <a name="which-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Vilka funktioner stöds i den offentliga förhandsversionen i Azure-brandvägg?  

* Tillståndskänslig brandvägg som en tjänst
* Inbyggd hög tillgänglighet med obegränsad skalbarhet i molnet
* FQDN-filtrering 
* Regler för filtrering av nätverkstrafik
* Stöd för utgående SNAT
* Möjligheten att centralt skapa, tillämpa och logga principer för program och nätverk-anslutning via Azure-prenumerationer och virtuella nätverk
* Fullständig integrering med Azure Monitor för loggning och analys 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Hur kan jag ansluta till den offentliga förhandsversionen Brandvägg för Azure?

Azure-brandväggen är för närvarande en hanterad förhandsversion som du kan ansluta till med hjälp av registrera-AzureRmProviderFeature PowerShell-kommando. Det här kommandot förklaras i dokumentationen för Azure-brandvägg offentlig förhandsversion.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Vad är priset för Azure-brandvägg?

Azure-brandväggen har en fast och varierande kostnad. Priserna är följande och ytterligare rabatt på 50% under förhandsversionen.

* Fast avgift: $1.25/firewall/hour
* Varierande avgift: $0.03/ GB som behandlas av brandväggen (inkommande eller utgående)

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

Förhandsversion av Azure brandväggen stöder endast utgående filtrering. Inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH eller FTP) är preliminärt planerat för Azure-brandvägg GA.  
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Vilka loggning och Analystjänster stöds av Azure-brandvägg?

Azure-brandväggen är integrerad med Azure Monitor för att visa och analysera loggar från brandväggen. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hubs. De kan analyseras i Log Analytics eller genom att olika verktyg som Excel och Power BI. Mer information finns i [självstudie: övervaka Azure brandväggsloggar](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hur fungerar Azure-brandväggen på olika sätt från befintliga tjänster, till exempel nva: er på marketplace?

Brandvägg för Azure är en enkel brandvägg-tjänst som kan lösa vissa kundscenarier med. Det är normalt att du har en blandning av tredjeparts-nva: er och Azure-brandvägg. Fungerar bättre tillsammans är prioriterat core.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure-brandvägg?

Web Application Firewall (WAF) är en funktion i Application Gateway som ger ett centraliserat inkommande skydd av dina webbappar mot vanliga kryphål och säkerhetsproblem. Azure-brandväggen ger utgående på nätverksnivå för alla portar och protokoll på programnivå skydd och för utgående HTTP/S. Inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP) är preliminärt planerat för Azure-brandvägg GA.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Vad är skillnaden mellan Nätverkssäkerhetsgrupper (NSG) och Azure-brandvägg?

Azure brandväggstjänsten kompletterar network security group funktioner. Tillsammans kan de ger bättre ”skydd på djupet” nätverkssäkerhet. Nätverkssäkerhetsgrupper innehåller distribuerade layer filtrering av nätverkstrafik för att begränsa trafik till resurser i virtuella nätverk i varje prenumeration. Azure-brandväggen är en fullständigt administrerad, centraliserad nätverk brandväggen som-tjänst, vilket ger nätverks - och programnivå skydd i olika prenumerationer och virtuella nätverk. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur ställer jag in Azure-brandvägg med min tjänstslutpunkter?

Vi rekommenderar tjänstslutpunkter för säker åtkomst till PaaS-tjänster. Du kan välja att aktivera Tjänsteslutpunkter i Azure-brandvägg undernät och inaktivera dem på anslutna virtuella ekernätverk. Det här sättet du dra nytta av funktioner – tjänsten endpoint säkerhets- och centrala loggning för all trafik.

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

### <a name="what-are-the-known-service-limits"></a>Vad kända gränser har tjänsten?

* Azure-brandväggen har en mjuk gräns på 1 000 TB per brandväggen per månad. 
* En instans av Azure-brandväggen som körs i ett centralt virtuella nätverk har virtuell nätverkspeering begränsningar med högst 50 virtuella ekernätverk.  
* Azure-brandväggen fungerar inte med global peering, så du bör ha minst en distribution av brandvägg per region.
* Azure-brandväggen har stöd för 10 k programmet regler och 10 k Nätverksregler.