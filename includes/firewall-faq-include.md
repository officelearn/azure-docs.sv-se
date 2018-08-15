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
ms.openlocfilehash: 5601f8d90f107636d2899a024772dccc8f75b69d
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "40131775"
---
### <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt administrerad brandvägg-som-tjänst-med inbyggd hög tillgänglighet och skalbarhet i obegränsad molnet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure-brandväggen är för närvarande i offentlig förhandsversion.

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Vilka funktioner stöds i den offentliga förhandsversionen i Azure-brandvägg?  

* Brandvägg som en tjänst
* Inbyggd hög tillgänglighet med obegränsad skalbarhet i molnet
* FQDN-filtrering 
* Regler för filtrering av nätverkstrafik
* Stöd för utgående SNAT
* Skapa, tillämpa och logga principer för program och nätverk-anslutning via Azure-prenumerationer och virtuella nätverk centralt
* Fullständig integrering med Azure Monitor för loggning och analys 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Hur kan jag ansluta till den offentliga förhandsversionen Brandvägg för Azure?

Azure-brandväggen är för närvarande en hanterad förhandsversion som du kan ansluta till med hjälp av kommandot registrera-AzureRmProviderFeature PowerShell enligt beskrivningen i Azure-brandväggen offentliga förhandsversion-dokumentationen.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Vad är priset för Azure-brandvägg?

Azure-brandväggen har en fast kostnad + rörlig kostnad. Priserna är nedan och dessa ytterligare rabatt på 50% under den allmänna förhandsvisningen.

* Fast avgift: $1.25/firewall/hour
* Varierande avgift: $0.03/ GB som behandlas av brandväggen (inkommande eller utgående).

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Vad är den typiska distributionsmodellen för Azure-brandvägg?

Det är möjligt att distribuera Azure-brandväggen på alla virtuella nätverk, skulle normalt kunder distribuera Azure-brandvägg i en central VNET och peer-andra virtuella nätverk till den i en nav och eker-modell. Du kan sedan ange standardvägen från peerkopplade virtuella nätverk så att den pekar till den här centrala brandväggen virtuellt nätverk.

### <a name="how-can-i-install-the-azure-firewall"></a>Hur kan jag installera Azure-brandvägg?

Azure-brandväggen kan konfigureras via Azure portal, PowerShell, REST API eller mallar. Se [självstudie: distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](../articles/firewall/tutorial-firewall-deploy-portal.md) stegvisa instruktioner.

### <a name="what-are-some-azure-firewall-concepts"></a>Vilka är några begrepp för Azure-brandvägg?

Azure-brandväggen har stöd för regler och regelsamlingar. En regelsamling är en uppsättning regler som delar samma ordning och prioritet. Regelsamlingar utförs i deras prioritetsordning, Network regelsamlingar har högre prioritet än program regelsamlingar, avslutas på alla regler.
Det finns två typer av samlingar:

* Regler för program: kan du konfigurera fullständigt kvalificerade domännamn (FQDN) som kan nås från ett undernät. 
* Network regler: kan du konfigurera regler som innehåller käll-adress, protokoll, målport och måladress. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Stöder Azure Brandvägg för inkommande trafikfiltrering?

Förhandsversion av Azure brandväggen stöder endast utgående filtrering. Inkommande skydd för icke-HTTP/S-protokoll (ex: RDP, SSH, FTP) preliminärt är planerat för Azure-brandväggen GA.  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Vilka loggning/analyser som stöds av Azure-brandvägg?

Azure-brandväggen är integrerad med Azure Monitor för att visa och analysera loggar från brandväggen. Loggar kan skickas till Log Analytics, Azure Storage eller Event Hub. De kan analyseras i Log Analytics eller genom att olika verktyg som Excel och Power BI. Se [självstudie: övervaka Azure brandväggsloggar](../articles/firewall/tutorial-diagnostics.md) för mer information.

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Hur fungerar Azure-brandvägg i förhållande till befintliga som nva: er på marketplace?

Brandvägg för Azure är en enkel brandvägg-tjänst som kan lösa vissa kundscenarier med. Det är normalt att du har en blandning av tredjeparts-nva: er och Azure-brandvägg. Fungerar bättre tillsammans är prioriterat core.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Vad är skillnaden mellan Application Gateway WAF och Azure-brandvägg?

Web Application Firewall (WAF) är en funktion i Application Gateway som ger ett centraliserat inkommande skydd av dina webbappar mot vanliga kryphål och säkerhetsproblem. Azure-brandväggen ger utgående skydd på nätverksnivå för alla portar och protokoll och program på skydd för utgående HTTP/S. Inkommande skydd för icke-HTTP/S-protokoll (till exempel RDP, SSH, FTP) är preliminärt planerat för Azure-brandvägg GA.

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>Vad är skillnaden mellan Nätverkssäkerhetsgrupper (NSG) och Azure-brandvägg?

Azure brandväggstjänsten kompletterar funktioner för Nätverkssäkerhetsgruppen och ger tillsammans bättre skydd på djupet nätverkssäkerhet. NSG: er ger distribuerade layer filtrering av nätverkstrafik för att begränsa trafik till resurser i virtuella nätverk i varje prenumeration.  Azure-brandväggen är en fullständigt administrerad, centraliserad nätverk brandväggen as-a-service, vilket ger nätverk och skydd på nätverksnivå över olika prenumerationer och virtuella nätverk (Vnet). 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hur ställer jag in Azure-brandvägg med min tjänstslutpunkter?

Vi rekommenderar tjänstslutpunkter för säker åtkomst till PaaS-tjänster. Brandvägg för Azure-kunder kan välja att aktivera Tjänsteslutpunkter i Azure-brandvägg undernät och inaktivera det på de anslutna virtuella ekernätverken för drar nytta av båda funktionerna i tjänsten slutpunktssäkerhet och centrala loggning för all trafik.

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

* Azure-brandväggen har en mjuk högst 1000 TB/brandvägg/månad. 
* Azure-brandväggen som körs i en central VNET kan komma att VNET peering begränsningar: max 50 virtuella ekernätverken.  
* Azure-brandväggen fungerar inte med global peering, så du bör ha minst en distribution av brandvägg per region.
* Azure har stöd för 10k programmet brandväggsregler och 10 k Nätverksregler.