---
title: "Azure stacken datacenter integrering – publicera slutpunkter"
description: "Lär dig hur du publicerar Azure Stack slutpunkter i ditt datacenter"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/18/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 0d15252079b62f6a74a1279309fb9b1b3ed5711e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure stacken datacenter integrering – publicera slutpunkter

*Gäller för: Azure Stack integrerat system*

Azure-stacken ställer in olika slutpunkter (VIP - virtuella IP-adresser) för dess infrastrukturroller. Dessa virtuella IP-adresser tilldelas från den offentliga IP-adresspoolen. Varje VIP är skyddad med en åtkomstkontrollista (ACL) i nätverkslagret för programvarudefinierade. ACL: er används också för de fysiska växlarna (TORs och BMC) för att ytterligare skydda lösningen. En DNS-post skapas för varje slutpunkt i externa DNS-zonen som angavs vid tidpunkten för distribution.


Arkitekturdiagram för följande visar olika nätverk lager och ACL: er:

![Arkitekturdiagram](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portar och protokoll (inkommande)

VIP infrastruktur som krävs för publicering Azure Stack-slutpunkter till externa nätverk visas i följande tabell. I listan visas varje slutpunkt, krävs port och protokoll. Slutpunkter som krävs för ytterligare resursproviders som SQL-resursprovidern och andra, beskrivs i dokumentationen för specifik resurs-providern.

Intern infrastruktur för VIP visas inte eftersom de inte krävs för att publicera Azure-stacken.

> [!NOTE]
> Användaren VIP: er är dynamisk, definieras användarna själva har ingen kontroll av Azure Stack-operatorn.


|Slutpunkt (VIP)|En post för DNS-värdpost|Protokoll|Portar|
|---------|---------|---------|---------|
|AD FS|`Adfs.[Region].[External FQDN]`|HTTPS|443|
|Portalen (administratör)|`Adminportal.[Region].[External FQDN]`|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (administratör)|`Adminmanagement.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Portalen (användare)|`Portal. [Region].[External FQDN]`|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (användare)|`Management.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Graph|`Graph.[Region].[External FQDN]`|HTTPS|443|
|Lista över återkallade certifikat|`Crl.[Region].[External FQDN]`|HTTP|80|
|DNS|`*.[Region].[External FQDN]`|TCP OCH UDP|53|
|Key Vault (användare)|`*.vault.[Region].[External FQDN]`|TCP|443|
|Key Vault (administratör)|`*.adminvault.[Region].[External FQDN]`|TCP|443|
|Storage-kö|`*.queue.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|Tabell för lagring|`*.table.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|Lagringsblob|`*.blob.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Portar och URL: er (utgående)

Azure-stacken stöder endast transparent proxy-servrar. I en distribution där en transparent proxy överordnade länkar till en traditionell proxyserver måste du tillåta följande portar och URL: er för utgående kommunikation:


|Syfte|URL: EN|Protokoll|Portar|
|---------|---------|---------|---------|
|Identitet|`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`|HTTP<br>HTTPS|80<br>443|
|Marketplace-syndikeringsfeed|`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>`https://*.microsoftazurestack.com`|HTTPS|443|
|Korrigering & uppdatering|`https://*.azureedge.net`|HTTPS|443|
|Registrering|`https://management.azure.com`|HTTPS|443|
|Användning|`https://*.microsoftazurestack.com`<br>`https://*.trafficmanager.com`|HTTPS|443|

## <a name="firewall-publishing"></a>Brandvägg för publicering

Portarna som listas ovan gäller för inkommande kommunikation när du publicerar Azure Stack-tjänster via en befintlig brandvägg.

Vi rekommenderar att du använder en enhet för att säkra Azure-stacken. Det är dock inte policyn. Även om brandväggar kan hjälpa dig med sådant som distribuerade denial of service (DDOS) attacker och innehållsgranskning, blir de också en genomströmning flaskhals för Azure storage-tjänster som blobbar, tabeller och köer.

Baserat på identitetsmodellen (Azure AD eller AD FS), kan eller inte kan krävas för att publicera AD FS-slutpunkten. Om en frånkopplad distributionsläget används, måste du publicera AD FS-slutpunkten. (Mer information finns i avsnittet Datacenter integration identitet).

Azure Resource Manager (administratör), administratörsportalen och Key Vault (administratör) slutpunkter kräver inte nödvändigtvis externa publicering. Det beror på scenariot. Till exempel som en tjänsteleverantör vilja du begränsa risken för angrepp och administrera endast Azure-paketet i nätverket och inte från Internet.

Det externa nätverket kan vara befintliga företagsnätverket för en enterprise-organisation. I ett sådant scenario, måste du publicera dessa slutpunkter för att fungera Azure Stack från företagsnätverket.

## <a name="edge-firewall-scenario"></a>Edge brandväggen scenario

I en kant-distribution distribueras Azure Stack direkt bakom gränsroutern (som tillhandahålls av Internetleverantören) med eller utan en brandvägg framför det.

![Arkitekturdiagram för en Azure-stacken edge-distribution](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

Normalt har offentligt dirigerbara IP-adresser angetts för den offentliga VIP-poolen vid tidpunkten för distribution i en kant-distribution. Det här scenariot kan en användare har fullständig själva kontrollerade molnet upplevelse som i ett offentligt moln som Azure.

### <a name="using-nat"></a>Med hjälp av NAT

Du rekommenderas inte på grund av arbetet kan du använda NAT (Network Address Translation) för publishing slutpunkter. Detta kräver en NAT-regel per användare VIP som innehåller alla portar som en användare kan använda för slutpunkt-publicering som fullständigt styrs av användare.

Ett annat övervägande är att Azure inte stöder att konfigurera en VPN-tunnel till en slutpunkt med NAT i ett scenario med hybridmoln med Azure.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Scenariot perimeter-Enterprise/intranät-brandväggen

Azure-stacken distribueras i ett perimeternätverk-enterprise/intranät-distribution, utöver andra brandvägg, som vanligtvis ingår i ett perimeternätverk (även kallat DMZ).

![Azure Stack brandväggen scenario](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Om offentligt dirigerbara IP-adresser har angetts för den offentliga VIP-poolen i Azure-stacken, tillhör perimeternätverket adresserna logiskt och kräver publiceringsregler i primära brandväggen.

### <a name="using-nat"></a>Med hjälp av NAT

Om du använder icke-offentligt dirigerbara IP-adresser för en offentlig VIP-pool i Azure-stacken, används NAT i sekundära brandväggen för att publicera Azure Stack-slutpunkter. I det här scenariot måste du konfigurera regler för serverpublicering på primära brandväggen utanför och på sekundära brandväggen. Tänk på följande om du vill använda NAT:

- NAT lägger till användning vid hantering av brandväggsregler eftersom användarna styra sina egna slutpunkter och sina egna regler för serverpublicering i stacken programvarudefinierade nätverksfunktioner (SDN). Användarna måste kontakta Azure-stacken operatorn att få sina VIP: er som har publicerats och uppdatera portlistan över.
- Vid användning av NAT begränsar användarupplevelsen, ger fullständig behörighet till operatorn över publiceringsförfrågningar.
- För hybridscenarion moln med Azure, Överväg att Azure inte stöder att konfigurera en VPN-tunnel till en slutpunkt som använder NAT.


## <a name="next-steps"></a>Nästa steg

[Integration av Azure Stack datacenter - säkerhet](azure-stack-integrate-security.md)