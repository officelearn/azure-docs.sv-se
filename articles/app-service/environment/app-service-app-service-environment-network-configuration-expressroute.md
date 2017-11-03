---
title: "Konfigurationsinformation för nätverket för att arbeta med Expressroute"
description: "Konfigurationsinformation för nätverket för att köra Apptjänstmiljöer i virtuella nätverk som är anslutna till en ExpressRoute-krets."
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: bb3e283e8a9327a9c66c8d8ded037cee5195ffc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Information om nätverkskonfiguration för App Service-miljöer med ExpressRoute
## <a name="overview"></a>Översikt
Kunder kan ansluta en [Azure ExpressRoute] [ ExpressRoute] krets på sina virtuella nätverksinfrastrukturen därför utöka sina lokala nätverk till Azure.  En Apptjänst-miljö kan skapas i ett undernät för det här [virtuellt nätverk] [ virtualnetwork] infrastruktur.  Appar som körs på Apptjänst-miljön kan sedan upprätta säkra anslutningar till backend-resurser som är tillgänglig endast med ExpressRoute-anslutningen.  

En Apptjänst-miljö kan skapas i **antingen** ett virtuellt nätverk i Azure Resource Manager **eller** klassisk distribution modellen virtuella nätverk.  Med en ändring görs i juni 2016 distribueras ASEs nu också till virtuella nätverk som använder offentliga-adressintervall eller RFC1918 adressutrymmen (d.v.s. privata adresser). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Nödvändiga nätverksanslutning
Det finns anslutningskrav för Apptjänstmiljöer som inte kanske ursprungligen uppfyllas i ett virtuellt nätverk som är anslutna till en ExpressRoute.  Apptjänstmiljöer kräver följande för att fungera korrekt:

* Utgående nätverksanslutning till Azure Storage-slutpunkterna över hela världen på båda portarna 80 och 443.  Detta inkluderar slutpunkter finns i samma region som Apptjänst-miljön, samt lagring slutpunkter finns i **andra** Azure-regioner.  Azure Storage-slutpunkter lösa under följande DNS-domäner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* och *file.core.windows.net*.  
* Utgående nätverksanslutning till tjänsten Azure-filer på port 445.
* Utgående nätverksanslutning till Sql DB-slutpunkter som finns i samma region som Apptjänst-miljön.  SQL DB slutpunkter lösa under följande domän: *database.windows.net*.  Detta kräver att öppna åtkomst till portarna 1433, 11000 11999 och 14000 14999.  Mer information finns [i den här artikeln på Sql Database V12 portar används](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Utgående nätverksanslutning till Azure management plan slutpunkter (både ASM och ARM-slutpunkter).  Detta inkluderar utgående anslutning till både *management.core.windows.net* och *management.azure.com*. 
* Utgående nätverksanslutning till *ocsp.msocsp.com*, *mscrl.microsoft.com* och *crl.microsoft.com*.  Detta krävs för att stödja SSL-funktioner.
* DNS-konfigurationen för det virtuella nätverket måste kunna matcha alla slutpunkter och domäner som nämns i tidigare återställningspunkter.  Om dessa slutpunkter inte kan matchas Apptjänstmiljö skapa försök misslyckas och befintliga Apptjänstmiljöer kommer att markeras som ohälsosam.
* Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar.
* Om en anpassad DNS-server finns på den andra änden av en VPN-gateway, måste DNS-servern vara nåbar från det undernät som innehåller Apptjänst-miljön. 
* Utgående sökvägen kan inte skickas genom interna företagets proxyservrar eller kan den vara force tunneldata till lokalt.  Gör det ändrar effektiva NAT-adress för utgående nätverkstrafik från Apptjänst-miljön.  Ändrar NAT-adress för en Apptjänstmiljö utgående nätverkstrafik kan anslutningsfel till många av de slutpunkter som anges ovan.  Detta resulterar i misslyckade försök för skapande av Apptjänst-miljö, samt tidigare felfri Apptjänstmiljöer markeras som ohälsosam.  
* Inkommande nätverksåtkomst till portar som krävs för Apptjänstmiljöer får enligt beskrivningen i det här [artikel][requiredports].

DNS-kraven kan uppfyllas genom att säkerställa att ett giltigt DNS-infrastrukturen är konfigurerad och underhålls för det virtuella nätverket.  Om DNS-konfigurationen har ändrats efter en Apptjänst-miljö har skapats av någon anledning, kan utvecklare tvinga en Apptjänst-miljö för den nya DNS-konfigurationen.  Utlöser en rullande miljö omstart med hjälp av ikonen ”Restart” som finns längst upp på bladet hantering av Apptjänst-miljö i det [Azure-portalen] [ NewPortal] kommer miljö för att hämta den nya DNS-konfigurationen.

Inkommande network access kraven kan uppfyllas genom att konfigurera en [nätverkssäkerhetsgruppen] [ NetworkSecurityGroups] i den Apptjänstmiljö undernät så att den nödvändiga åtkomsten enligt beskrivningen i det här [artikel][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Aktivera utgående nätverksanslutning för en Apptjänst-miljö
Som standard annonserar en nyligen skapade ExpressRoute-krets en standardväg som tillåter utgående Internet-anslutning.  En Apptjänst-miljö kommer att kunna ansluta till andra Azure-slutpunkter med den här konfigurationen.

Men en gemensam konfiguration av customer är att definiera egna standardvägen (0.0.0.0/0) som tvingar utgående Internet-trafiken flöda lokalt i stället.  Den här trafikflöde bryts utan undantag Apptjänstmiljöer eftersom utgående trafik är antingen blockerade lokalt eller NAT skulle med ett okänt uppsättning adresser som inte längre att fungera med olika Azure-slutpunkter.

Lösningen är att definiera en (eller flera) användardefinierade vägar (udr: er) för det undernät som innehåller Apptjänst-miljön.  En UDR definierar undernät-specifika vägar som ska användas i stället för standardväg.

Om möjligt bör du använder följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och som standard kraft tunnlar all utgående trafik lokalt.
* UDR tillämpad på undernätet som innehåller Apptjänst-miljön definierar 0.0.0.0/0 med ett nexthop-typ för Internet (ett exempel på detta finns längre ned i den här artikeln).

Den kombinerade effekten av dessa steg är att undernätverksnivån UDR företräde framför den ExpressRoute Tvingad tunneltrafik tillse utgående Internetåtkomst från Apptjänst-miljön.

> [!IMPORTANT]
> De vägar som definierats i en UDR **måste** vara specifikt nog att företräde framför alla vägarna som annonseras av ExpressRoute-konfigurationen.  Exemplet nedan använder adressintervallet bred 0.0.0.0/0 och som sådan kan potentiellt av misstag åsidosättas av flödet annonser med hjälp av mer specifika adressintervall.
> 
> Apptjänstmiljöer stöds inte med ExpressRoute-konfigurationer som **annonserar vägar från offentlig peering sökvägen att privat peering sökväg mellan**.  ExpressRoute-konfigurationer som har konfigurerats, offentlig peering får vägannonser från Microsoft för ett stort antal Microsoft Azure IP-adressintervall.  Om dessa adressintervall mellan annonseras i privat peering sökväg, är slutresultatet att alla utgående paket från den Apptjänstmiljö undernät kommer att framtvinga tunneldata kundens lokala nätverkets infrastruktur.  Det här flödet i nätverk stöds för närvarande inte med Apptjänstmiljöer.  En lösning på problemet är att stoppa mellan reklam vägar från offentlig peering sökvägen att privat peering sökväg.
> 
> 

Bakgrundsinformation om användardefinierade vägar är tillgänglig i det här [översikt][UDROverview].  

Information om hur du skapar och konfigurerar användardefinierade vägar är tillgänglig i det här [hur till guiden][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Exempel på UDR konfiguration för en Apptjänst-miljö
**Förutsättningar**

1. Installera Azure Powershell från den [Azure hämtar sidan] [ AzureDownloads] (funktionsdokumentationen juni 2015 eller senare).  Under ”kommandoradsverktyg” finns det en länk för ”installera” under ”Windows Powershell” som ska installera det senaste Powershell-cmdlet.
2. Vi rekommenderar att du skapat ett unikt undernät för exklusiv användning av en Apptjänst-miljö.  Detta säkerställer att udr: er som tillämpas på undernätet bara öppnas utgående trafik för Apptjänst-miljön.
3. **Viktiga**: Distribuera inte Apptjänstmiljö förrän **när** följande konfigurationssteg följs.  Detta säkerställer att utgående nätverksanslutningen är tillgänglig innan du försöker att distribuera en Apptjänst-miljö.

**Steg 1: Skapa en namngiven routningstabell**

Följande kodutdrag skapar en routningstabell som kallas ”DirectInternetRouteTable” i West US Azure-region:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Steg 2: Skapa en eller flera vägar i routningstabellen**

Du behöver lägga till en eller flera vägar i routningstabellen för att aktivera utgående Internetåtkomst.  

Den rekommenderade metoden för att konfigurera utgående åtkomst till Internet är att definiera en väg för 0.0.0.0/0 som visas nedan.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Kom ihåg att 0.0.0.0/0 är en bred adressintervallet och därmed åsidosätts av mer specifika adressintervall annonseras av expressroute har skapats.  Om du vill för att iterera tidigare rekommendationen, bör en UDR med en 0.0.0.0/0 väg användas tillsammans med en konfiguration för ExressRoute som bara annonserar samt 0.0.0.0/0. 

Alternativt kan hämta du en omfattande och uppdaterad lista över CIDR-intervallen som används av Azure.  Xml-filen som innehåller alla Azure IP-adressintervall som är tillgängliga från den [Microsoft Download Center][DownloadCenterAddressRanges].  

Observera dock att dessa områden som ändras med tiden, vilket kräver periodiska manuell uppdatering av de användardefinierade vägarna att synkronisera.  Dessutom eftersom det är en standard övre gräns på 100 vägar i en enda UDR, annonseras behöver du ”sammanfatta” Azure IP-adressintervall ska rymmas inom gränsen 100 väg i åtanke att UDR definierats vägar måste vara mer specifik än vägar av din ExpressRoute.  

**Steg 3: Koppla routningstabellen undernät som innehåller Apptjänst-miljön**

Det sista konfigurationssteget är att koppla routningstabellen för undernätet där Apptjänst-miljön ska distribueras.  Följande kommando associerar ”DirectInternetRouteTable” till den ”ASESubnet” som slutligen innehåller en Apptjänst-miljö.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Steg 4: Sista stegen**

När routningstabellen är bunden till undernätet, rekommenderas det att först testa och bekräfta den avsedda effekten.  Till exempel distribuera en virtuell dator i undernätet och kontrollera att:

* Utgående trafik till både Azure och Azure-slutpunkter som nämnts tidigare i den här artikeln är **inte** flödar ned ExpressRoute-kretsen.  Det är viktigt att verifiera det här beteendet eftersom om utgående trafik från undernätet som är fortfarande tvingas tunneldata lokalt, Apptjänst-miljö skapas kommer alltid att misslyckas. 
* DNS-sökning för slutpunkter som nämnts tidigare alla löser korrekt. 

När ovanstående steg bekräftas måste du ta bort den virtuella datorn eftersom undernätet måste vara ”tom” Apptjänst-miljön har skapats.

Fortsätt sedan med att skapa en Apptjänst-miljö.

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [introduktion till Apptjänst-miljö][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
