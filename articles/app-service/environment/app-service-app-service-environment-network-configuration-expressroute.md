---
title: Information om nätverkskonfiguration för att arbeta med Express Route
description: Information om nätverkskonfiguration för att köra App Service-miljöer i virtuella nätverk är anslutna till en ExpressRoute-krets.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: 7873192e4a66cd2faed5a1a1255377139d33d750
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616069"
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Information om nätverkskonfiguration för App Service-miljöer med ExpressRoute
## <a name="overview"></a>Översikt
Kunder kan ansluta en [Azure ExpressRoute] [ ExpressRoute] kretsen till sina virtuell nätverksinfrastruktur, vilket utöka sina lokala nätverk till Azure.  En App Service Environment kan skapas i ett undernät för det här [virtuellt nätverk] [ virtualnetwork] infrastruktur.  Appar som körs på App Service Environment kan upprätta säkra anslutningar till backend-resurser som är tillgängliga om de endast via ExpressRoute-anslutning.  

Du kan skapa en App Service Environment i **antingen** ett Azure Resource Manager-nätverk **eller** ett virtuellt nätverk för klassisk distribution-modellen.  Med en ny ändring som gjorts i juni 2016, kan du nu även distribuera ase-miljöer i virtuella nätverk som använder offentliga adressintervall eller RFC1918 adressutrymmen (d.v.s. privata adresser). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Krävs nätverksanslutning
Det finns anslutningskrav för App Service-miljöer som inte kanske inledningsvis uppfyllas i ett virtuellt nätverk som är anslutna till en ExpressRoute.  App Service-miljöer kräver följande för att fungera korrekt:

* Utgående nätverksanslutning till Azure Storage-slutpunkter i hela världen på båda portarna 80 och 443.  Detta inkluderar slutpunkter som finns i samma region som App Service Environment, samt storage-slutpunkter som finns i **andra** Azure-regioner.  Azure Storage-slutpunkter lösa under följande DNS-domäner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* och *file.core.windows.net*.  
* Utgående nätverksanslutning till Azure Files-tjänsten på port 445.
* Utgående nätverksanslutning till Sql DB-slutpunkter som finns i samma region som App Service Environment.  SQL DB slutpunkter lösa under följande domän: *database.windows.net*.  Detta kräver att öppna åtkomst till portarna 1433, 11000 11999 och 14000 14999.  Mer information finns i [den här artikeln om Sql Database V12 portanvändning](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Utgående nätverksanslutning (både ASM och ARM-slutpunkter) för slutpunkter i Azure management-plan.  Detta inkluderar utgående anslutning till båda *management.core.windows.net* och *management.azure.com*. 
* Utgående nätverksanslutning till *ocsp.msocsp.com*, *mscrl.microsoft.com* och *crl.microsoft.com*.  Detta behövs för att stödja SSL-funktionen.
* DNS-konfiguration för det virtuella nätverket måste kunna lösa alla slutpunkter och domäner som nämns i de tidigare punkterna.  Om dessa slutpunkter inte kan matchas, App Service Environment skapas försök att misslyckas och befintliga App Service Environment kommer att markeras som felaktig.
* Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar.
* Om en anpassad DNS-server finns på den andra änden av en VPN-gateway, måste DNS-server kunna nås från undernätet som innehåller App Service Environment. 
* Utgående sökvägen kan inte skickas genom interna företagets proxyservrar och inte heller kan det vara tvingande till en lokal.  Detta ändrar den effektiva NAT-adressen för utgående nätverkstrafik från App Service Environment.  Ändra NAT-adressen för en App Service Environment utgående nätverkstrafik kommer att orsaka anslutningsfel till många av de slutpunkter som anges ovan.  Detta resulterar i misslyckade försök för skapande av App Service Environment, samt tidigare felfri App Service-miljöer som markeras som felaktig.  
* Inkommande nätverksåtkomst till portar som krävs för App Service-miljöer måste tillåtas enligt beskrivningen i det här [artikeln][requiredports].

DNS-krav kan uppfyllas genom att kontrollera att en giltig DNS-infrastruktur konfigurerad och underhållen för det virtuella nätverket.  Om DNS-konfigurationen har ändrats efter en App Service Environment har skapats av någon anledning, kan utvecklare tvinga en App Service Environment att den använder den nya DNS-konfigurationen.  Utlösa en löpande miljö omstart med hjälp av ”Restart”-ikonen finns högst upp på bladet för hantering i App Service Environment i den [Azure-portalen] [ NewPortal] leder till miljön att hämta nya DNS konfiguration.

Inkommande åtkomstkraven kan uppfyllas genom att konfigurera en [nätverkssäkerhetsgrupp] [ NetworkSecurityGroups] i App Service Environment undernät så att åtkomsten som krävs enligt beskrivningen i det här [ artikeln][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Aktivera utgående nätverksanslutning för en App Service Environment
Som standard annonserar en nyligen skapade ExpressRoute-krets en standardväg som tillåter utgående Internet-anslutning.  En App Service Environment kommer att kunna ansluta till andra Azure-slutpunkter med den här konfigurationen.

Men en vanlig kund-konfiguration är att definiera egna standardväg (0.0.0.0/0) vilket tvingar utgående Internet-trafiken flöda lokalt i stället.  Det här flödet i nätverkstrafiken delar App Service-miljöer utan undantag eftersom den utgående trafiken är antingen blockerade lokalt eller NAT skulle med ett okänt uppsättning adresser som inte längre att fungera med olika Azure-slutpunkter.

Lösningen är att definiera en (eller mer) användardefinierade vägar (Udr) i det undernät som innehåller App Service Environment.  En UDR definierar undernät-specifika vägar som kommer att användas i stället för standardväg.

Om det är möjligt rekommenderar vi att du använder du följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och som standard tunnlar tvinga all utgående trafik lokalt.
* Den användardefinierade vägen som tillämpas på undernät som innehåller App Service Environment definierar 0.0.0.0/0 med ett nexthop-typen Internet (ett exempel på detta finns längre ned i den här artikeln).

Den kombinerade effekten av de här stegen är att undernätverksnivån UDR har företräde över ExpressRoute Tvingad tunneltrafik, vilket säkerställer utgående Internetåtkomst från App Service Environment.

> [!IMPORTANT]
> Vägarna som definieras i en UDR **måste** vara tillräckligt specifika för att få företräde framför eventuella vägar som annonseras av ExpressRoute-konfigurationen.  Exemplet nedan använder det breda adressintervallet 0.0.0.0/0 och därför kan eventuellt åsidosättas av misstag av vägannonseringar som använder mer specifika adressintervall.
> 
> App Service-miljöer stöds inte med ExpressRoute-konfigurationer som **korsannonserar vägar från den offentliga peering-sökvägen till den privata peering-sökvägen**.  ExpressRoute-konfigurationer som har offentlig peering har konfigurerats, kommer få vägannonseringar från Microsoft för ett stort antal Microsoft Azure IP-adressintervall.  Om dessa adressintervallen korsannonseras på den privata peering-sökvägen, är slutresultatet att alla utgående nätverkspaket från undernätet för App Service Environment att tvingas med tunneltrafik i en kunds lokala nätverksinfrastruktur.  Det här nätverksflödet stöds för närvarande inte i App Service Environment.  En lösning på problemet är att stoppa korsannonsering vägar från den offentliga peering-sökvägen till den privata peering-sökvägen.
> 
> 

Bakgrundsinformation om användardefinierade vägar finns i den här [översikt][UDROverview].  

Information om hur du skapar och konfigurerar användardefinierade vägar finns i den här [hur till guiden][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>UDR exempelkonfiguration för App Service Environment
**Förutsättningar**

1. Installera Azure Powershell från den [Azure hämtar sidan] [ AzureDownloads] (funktionsdokumentationen juni 2015 eller senare).  Under ”kommandoradsverktyg” finns det en länk för ”installera” under ”Windows Powershell” som ska installera det senaste Powershell-cmdlet.
2. Vi rekommenderar att ett unikt undernät skapas för exklusiv användning av en App Service Environment.  Detta säkerställer att udr: er som tillämpas på undernätet öppnas bara utgående trafik för App Service Environment.
3. **Viktiga**: inte distribuerar en App Service Environment tills **när** följande konfigurationssteg följs.  Detta säkerställer att utgående nätverksanslutning är tillgänglig innan du distribuerar en App Service Environment.

**Steg 1: Skapa en namngiven routningstabell**

Följande fragment skapar en routningstabell som kallas ”DirectInternetRouteTable” i regionen västra USA Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Steg 2: Skapa en eller flera vägar i routningstabellen**

Du behöver att lägga till en eller flera vägar i routningstabellen för att aktivera utgående Internetåtkomst.  

Den rekommenderade metoden för att konfigurera utgående åtkomst till Internet är att definiera en väg för 0.0.0.0/0 som visas nedan.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Kom ihåg att 0.0.0.0/0 är en bred adressintervall och därmed åsidosätts av mer specifika adressintervall som annonseras av ExpressRoute.  En UDR med en väg med 0.0.0.0/0 bör användas tillsammans med en ExpressRoute-konfiguration som bara annonserar 0.0.0.0/0 samt för att iterera tidigare rekommendationen igen. 

Alternativt kan hämta du en omfattande och uppdaterad lista över CIDR-intervall som används av Azure.  Xml-filen som innehåller alla Azure-IP-adressintervall är tillgänglig från den [Microsoft Download Center][DownloadCenterAddressRanges].  

Observera att dessa områden som ändras med tiden, vilket kräver regelbundna manuella uppdateringar till användardefinierade vägar att synkronisera.  Dessutom eftersom det inte finns en standard övre gräns på 100 vägar i en enda UDR, annonseras behöver du ”summarize” Azure IP-adressintervall ska rymmas inom gränsen 100 vägen i åtanke att UDR definierade vägar måste vara mer specifik än vägarna av din ExpressRo ute.  

**Steg 3: Associera routningstabellen för undernätet som innehåller App Service Environment**

Det sista konfigurationssteget är att associera routningstabellen till undernätet där App Service Environment ska distribueras.  Följande kommando kopplar ”DirectInternetRouteTable” till den ”ASESubnet” som så småningom innehåller en App Service Environment.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Steg 4: Sista stegen**

När routningstabellen är bunden till undernätet, rekommenderas det att först testa och kontrollera den avsedda effekten.  Till exempel distribuera en virtuell dator i undernätet och bekräfta att:

* Utgående trafik till både Azure och Azure-slutpunkter som nämnts tidigare i den här artikeln är **inte** flödar ned ExpressRoute-kretsen.  Det är mycket viktigt att verifiera det här beteendet, eftersom om utgående trafik från undernätet är fortfarande Tvingad tunneltrafik lokalt, App Service Environment inte alltid att skapa. 
* DNS-sökning för slutpunkter som nämnts tidigare alla löser korrekt. 

När stegen ovan är bekräftad, måste du ta bort den virtuella datorn eftersom undernätet måste vara ”Töm” vid tidpunkten för App Service Environment har skapats.

Fortsätt sedan med att skapa en App Service Environment!

## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [introduktion till App Service Environment][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
